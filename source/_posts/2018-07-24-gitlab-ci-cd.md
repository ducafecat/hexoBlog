---
title: gitlab-ci-cd
top: false
date: 2018-07-24 13:25:31
tags: gitlab
categories:
thumbnail:
---

# GitLab CI CD

## 安装过程

### 安装 Docker Centos7

- 阿里镜像

```bash
# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3: 更新并安装 Docker-CE
sudo yum makecache fast
sudo yum -y install docker-ce
# Step 4: 开启Docker服务
sudo service docker start

# 注意：
# 官方软件源默认启用了最新的软件，您可以通过编辑软件源的方式获取各个版本的软件包。例如官方并没有将测试版本的软件源置为可用，你可以通过以下方式开启。同理可以开启各种测试版本等。
# vim /etc/yum.repos.d/docker-ce.repo
#   将 [docker-ce-test] 下方的 enabled=0 修改为 enabled=1
#
# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# yum list docker-ce.x86_64 --showduplicates | sort -r
#   Loading mirror speeds from cached hostfile
#   Loaded plugins: branch, fastestmirror, langpacks
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            @docker-ce-stable
#   docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable
#   Available Packages
# Step2 : 安装指定版本的Docker-CE: (VERSION 例如上面的 17.03.0.ce.1-1.el7.centos)
# sudo yum -y install docker-ce-[VERSION]
```

- 加速

```sh
sudo mkdir -p /etc/docker
sudo vi /etc/docker/daemon.json
{
  "registry-mirrors": ["https://8stycbeq.mirror.aliyuncs.com"]
}
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 安装 GitLab

```sh
sudo docker run --detach \
    --hostname localhost \
    --publish 6443:443 --publish 8081:80 --publish 6022:22 \
    --name cn-gitlab \
    --restart always \
    --volume /Users/soneone/Documents/labs/gitlab/config:/etc/gitlab \
    --volume /Users/soneone/Documents/labs/gitlab/logs:/var/log/gitlab \
    --volume /Users/soneone/Documents/labs/gitlab/data:/var/opt/gitlab \
    twang2218/gitlab-ce-zh:latest
```

- 登录初始密码

  - [localhost:8081](http://localhost:8081/)
  - root
  - 12345678

- 新建项目

  - [vueweb.git](http://localhost/root/vueweb.git)

### 安装 Runner

```sh
sudo wget -O /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

sudo chmod +x /usr/local/bin/gitlab-runner

curl -sSL https://get.docker.com/ | sh

sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash

sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
```

### 注册 Runner

- 获取 `Token`

![Token](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-07-24-14-17-41.png)

- 启动 Runner

- shell 方式

```sh
gitlab-runner register
```

- docker 方式

```sh
docker run --rm -t -i \
  -v /srv/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name gitlab-runner gitlab/gitlab-runner register

docker run --rm -t -i \
  -v /Users/hans/Documents/labs/gitlab-runner/config:/etc/gitlab-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name gitlab-runner gitlab/gitlab-runner register \
  --non-interactive \
  --executor "docker" \
  --docker-image alpine:3 \
  --url "http://localhost:8081/" \
  --registration-token "3VQFmdddpVinQhqpYz8N" \
  --description "docker-runner" \
  --tag-list "docker,my,test" \
  --run-untagged \
  --locked="false"
```

## 参考

- [Install GitLab Runner manually on GNU/Linux](https://docs.gitlab.com/runner/install/linux-manually.html)
- [Registering Runners](https://docs.gitlab.com/runner/register/index.html)
- [Docker CE 镜像源站](https://yq.aliyun.com/articles/110806?spm=5176.8351553.0.0.151c19911eJVWX)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
