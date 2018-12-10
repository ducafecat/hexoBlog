---
title: gitlab + gitlab-runner 集成 CICD
top: false
date: 2018-08-18 13:25:31
tags: gitlab
categories: devOps
thumbnail:
---

# gitlab + gitlab-runner 集成 CICD

## 1 gitlab-runner 安装

- 1.1 下载执行文件

```sh
# Linux x86-64
sudo wget -O /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

# Linux x86
sudo wget -O /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-386

# Linux arm
sudo wget -O /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm
```

- 1.2 设置执行权限

```sh
sudo chmod +x /usr/local/bin/gitlab-runner
```

- 1.3 创建 GitLab CI 用户

```sh
useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
```

- 1.4 运行服务

```sh
gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
gitlab-runner install --user=root --working-directory=/root/gitlab-runner
gitlab-runner start
```

## 2 gitlab-runner 注册

### 2.1 获取 Gitlab `注册令牌`

**打开 gitlab 项目 -> 设置 -> CI / CD -> Runners 设置**

![Token](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-07-24-14-17-41.png)

### 2.2 LINUX 注册

- 运行注册

```sh
sudo gitlab-runner register
```

- 输入你的 GitLab URL

```sh
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
https://xxx.xxx
```

- 输入 `注册令牌` 来注册 Runner

```sh
Please enter the gitlab-ci token for this runner
xxx
```

- 输入 Runner 说明

```sh
Please enter the gitlab-ci description for this runner
[hostame] my-runner
```

- 输入 Runner 的 tags

```sh
Please enter the gitlab-ci tags for this runner (comma separated):
my-tag,another-tag
```

- 输入 Runner 执行方式

```sh
Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
shell
```

- 如果是在 Docker 中运行, you'll be asked for the default image to be used for projects that do not define one in .gitlab-ci.yml:

```sh
Please enter the Docker image (eg. ruby:2.1):
alpine:latest
```

## 3 链接成功

### 3.1 runner 列表

![ducafecat_2018-08-17-15-16-23](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-16-23.png)

### 3.2 runner 修改

![ducafecat_2018-08-17-15-17-48](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-17-48.png)

## 4 编写 `.gitlab-ci.yml` 集成

```yml
image: java:8
stages:
  - build
  - build-pro

#测试环境
build:
  stage: build
  script:
  - pwd
  - cd project/my_app
  - /usr/local/maven/bin/mvn clean package -P test
  - sh /usr/local/project_deploy_script/app/app_test.sh
  tags:
  - deploy-app
  only:
  - develop
#正式环境
  stage: build-pro
  script:
  - pwd
  - cd project/my_app
  - /usr/local/maven/bin/mvn clean package -P pro
  - sh /usr/local/project_deploy_script/app/app.sh
  tags:
  - deploy-app
  only:
  - master
```

## 5 执行集成

- 下次提交代码就会走集成任务了

![ducafecat_2018-08-17-15-26-05](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-26-05.png)

- 任务阶段

![ducafecat_2018-08-17-15-27-32](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-27-32.png)

- 详情

![ducafecat_2018-08-17-15-29-48](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-29-48.png)

![ducafecat_2018-08-17-15-30-38](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-30-38.png)

## 参考

- [Install GitLab Runner manually on GNU/Linux](https://docs.gitlab.com/runner/install/linux-manually.html)
- [Registering Runners](https://docs.gitlab.com/runner/register/index.html)
- [Docker CE 镜像源站](https://yq.aliyun.com/articles/110806?spm=5176.8351553.0.0.151c19911eJVWX)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
