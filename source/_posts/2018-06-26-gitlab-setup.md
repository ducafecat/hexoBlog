---
title: gitlab-setup
top: false
date: 2018-06-26 17:34:26
tags: gitlab
categories:
thumbnail:
---

# 介绍

## 环境

## 安装

### 1 拉取镜像

```bash
docker search gitlab
```

![ducafecat_2018-06-26-17-38-41](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-06-26-17-38-41.png)

> gitlab/gitlab-ce
> twang2218/gitlab-ce-zh

### 2 启动镜像

```bash
社区版本
sudo docker run --detach \
    --hostname localhost \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /Users/hans/Documents/labs/docker/gitlab/config:/etc/gitlab \
    --volume /Users/hans/Documents/labs/docker/gitlab/logs:/var/log/gitlab \
    --volume /Users/hans/Documents/labs/docker/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest

社区汉化版
sudo docker run --detach \
    --hostname localhost \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name cn-gitlab \
    --restart always \
    --volume /Users/hans/Documents/labs/docker/gitlab/config:/etc/gitlab \
    --volume /Users/hans/Documents/labs/docker/gitlab/logs:/var/log/gitlab \
    --volume /Users/hans/Documents/labs/docker/gitlab/data:/var/opt/gitlab \
    twang2218/gitlab-ce-zh:latest
```

## 问题

### centos7中docker文件挂载，容器中没有执行权限

```bash
1，在运行容器的时候，给容器加特权：
示例：docker run -i -t --privileged=true -v /home/docs:/src waterchestnut/nodejs:0.12.0

2，临时关闭selinux：
示例：su -c "setenforce 0"
注意：之后要记得重新开启selinux，命令：su -c "setenforce 1"

3. 添加selinux规则，将要挂载的目录添加到白名单：
chcon -Rt svirt_sandbox_file_t /home/docs
```

## 参考

* [gitlab](https://gitlab.com/)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
