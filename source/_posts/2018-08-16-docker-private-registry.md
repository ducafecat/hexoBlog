---
title: docker 私仓建设 Registry + Portainer
top: false
date: 2018-08-16 09:41:04
tags: docker
categories: Docker
thumbnail: http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-14-10-56-05.png
---

# docker 私仓建设 Registry + Portainer

## 环境说明

- 服务器
  - centos 7.4
  - ip 172.0.0.10

- 客户端
  - centos 7.4
  - macos
  - windows

## 1 服务端

### 1.1 运行 `registry` 服务

```sh
docker run -d -p 5000:5000 --restart always \
  -v /root/docker-registry/data/registry:/var/lib/registry \
  -v /root/docker-registry/config.yml:/etc/docker/registry/config.yml \
  --name my-registry registry:latest
```

### 2.1 修改配置 `config.yml`

```js
version: 0.1
log:
  fields:
    service: registry
storage:
  delete:
    enabled: true
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3
```

## 2 客户端

### 2.1 改仓库地址

- centos

```sh
vim  /usr/lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd  --insecure-registry 172.0.0.10:5000

systemctl daemon-reload
systemctl restart docker
```

- macos

![macos ExecStart](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-14-52-36.png)

### 2.2 打包上传

```sh
docker build -t 172.17.0.10:5000/my-app:1.0.1 .
docker push 172.17.0.10:5000/my-app:1.0.1
```

- 修改 `tag` 服务器上保留一个 `latest` 版本

```sh
docker tag 172.17.0.10:5000/my-app:1.0.1 172.17.0.10:5000/my-app:latest
docker push 172.17.0.10:5000/my-app:latest
```

- 查看

  - 镜像列表 `http://172.17.0.10:5000/v2/_catalog`
  - 镜像详情 `http://172.17.0.10:5000/v2/image_name/tags/list`

### 2.3 下拉镜像

```sh
docker pull 172.17.0.10:5000/my-app:1.0.1
```

### 2.4 删除镜像

- 镜像位置、sha256

```sh
cat /root/docker-registry/data/registry/docker/registry/v2/repositories/my-app/_manifests/tags/1.0.1/current

sha256:0599d30a8dc59cb316f8024db1490fa38aa7f98739d73afb28eb6f1d1655bb46[root@vm03 current]# pwd
```

- 删除镜像对应的API如下

```sh
DELETE /v2/<name>/manifests/<reference>
name:镜像名称
reference: 镜像对应sha256值
```

```sh
curl -I -X DELETE http://172.17.0.10:5000/v2/my-app/manifests/sha256:6a67ba482a8dd4f8143ac96b1dcffa5e45af95b8d3e37aeba72401a5afd7ab8e
```

- 查看数据大小

```sh
docker exec -it f70d0c79e6d546d4 sh
~ # du  -chs  /var/lib/registry/
182.4M  /var/lib/registry/
182.4M  total
```

- 进行容器执行垃圾回收命令

```sh
registry garbage-collect /etc/docker/registry/config.yml
```

- 批量删除镜像

```sh
docker rmi -f $(docker images | grep my-app)
```

## 3 可视化工具 `Portainer`

### 3.1 运行服务

```sh
docker run -d -p 9100:9000 \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --name prtainer-local \
    portainer/portainer
```

### 3.2 主界面

![Portainer](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-14-58-46.png)

### 3.3 容器管理界面

![Container list](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-15-09-08.png)

### 3.4 镜像列表

![Image list](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-15-14-52.png)

### 3.5 网络列表

![Network list](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-15-15-38.png)

## 4 参考

- [Docker Registry](https://docs.docker.com/registry/)
- [Portainer](https://hub.docker.com/r/portainer/portainer/)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
