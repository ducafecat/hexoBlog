---
title: Docker 01-常用指令
top: false
date: 2018-06-14 10:15:06
tags: docker
categories:
thumbnail:
---

# Docker 01-常用指令

## 命令

### 拉取镜像

```bash
sudo docker pull centos:6.9
```

[centos 镜像列表](https://store.docker.com/images/centos)

### 配置 Docker 中国区官方镜像

```json
vi /etc/docker/daemon.json
{
  "registry-mirrors": [ "https://registry.docker-cn.com"]
}
systemctl daemon-reload
systemctl restart docker
```

### 镜像列表

```bash
sudo docker images
```

### 从镜像到容器

```bash
sudo docker run -ti --name first centos:6.9 bash

Docker run 是从一个镜像运行一个容器的指令。
-ti 参数的含义是：terminal interactive，这个参数可以让我们进入容器的交互式终端。
--name 指定容器的名字，后面的 first 就是我们给这个容器起的名字。
centos:6.9 是致命从哪个镜像运行容器，ubuntu是仓库名，16.04是标签。
bash 指明我们使用 bash 终端。
```

### 容器列表

```bash
sudo docker ps -a
```

### 容器提交为一个新的镜像

```bash
sudo docker commit first my_image:v1.0
```

### 后台运行

```bash
sudo docker run -d --name withD centos:6.9 bash -c "echo hello world"
```

### 删除容器

```bash
sudo docker rm nameOfContainer
```

### 进入容器 exec

```bash
sudo docker run -d -ti --name background centos:6.9 bash
sudo docker exec -ti background bash
```

> exec 的过程其实是给容器新开了一个进程
> 在 exec 里面执行 exit 命令，你只是关掉了 exec 命令新开的进程，而主进程依旧在运行，所以容器并不会停止

### 进入容器 attach

```bash
sudo docker attach background
```

> 在 attach 里面运行 exit 命令，你实际是终止了主进程，所以容器也就随之被停止了

### 终止一个运行的容器

```bash
sudo docker kill nameOfContainer
```

### Docker 日志

```bash
sudo docker logs logtest
```

### 内存限制

参数 | 简介
-----|----------------
-m, - -memory     | 内存限制，格式：数字+单位，单位可以是b, k, m, g，最小4M
-- -memory-swap   | 内存和交换空间总大小限制，注意：必须比-m参数大

### CPU限制

参数 | 简介
-----|----------------
-- -cpuset-cpus=""      | 允许使用的CPU集
-c,- -cpu-shares=0      | CPU共享权值
-- -cpu-quota=0         | 限制CPU CFS配额，必须不小于1ms，即>=1000
cpu-period=0            | 限制CPU CFS调度周期，范围是100ms～1s,即[1000， 1000000]

```bash
sudo docker run -ti --cpuset-cpus="1,3" --name cpuset centos:6.9 bash
```

> 比如下面的指令指定容器进程可以在 CPU1 和 CPU3 上运行

### 查看容器详细信息

```bash
sudo docker inspect [nameOfContainer]
```

### 查看容器最近一个进程

```bash
sudo docker top [nameOfContainer]
```

### 停止一个正在运行的容器

```bash
sudo docker stop [nameOfContainer]
```

### 继续运行一个被停止的容器

```bash
sudo docker restart [nameOfContainer]
```

### 暂停一个容器进程

```bash
sudo docker pause [nameOfContainer]
```

### 取消暂停

```bash
sudo docker unpause [nameOfContainer]
```

### 终止一个容器

```bash
sudo docker kill [nameOfContainer]
```

### 删除镜像

```bash
sudo docker rmi [nameOfImage]
```

### 查看镜像操作记录

```bash
sudo docker history [nameOfImage]
```

### 给镜像设置一个新的仓库：版本对

```bash
sudo docker tag my_image:v1.0 my:v0.1
```

> 运行了上面的指令我们就得到了一个新的，和原来的镜像一模一样的镜像。

### 查看镜像详细信息

```bash
sudo docker inspect [nameOfImage]
```

### 搜索镜像

```bash
sudo docker login
sudo docker search ubuntu
```

## 数据卷

### 创建一个卷

```bash
docker run -ti --name volume1 -v /myDir centos:6.9 bash
```

### 删除一个数据卷

```bash
docker rm -v volume1
```

### 挂载宿主目录

```bash
docker run -ti --name volume1 -v ~/Documents/labs:/myDir centos:6.9 bash
```

### 数据卷容器

```bash
docker run -ti  -d -v /dataVolume --name v0 centos:6.9
docker run -ti --volumes-from v0 --name v1 centos:6.9 bash
```

## 网络

### 端口映射

```bash
docker commit -m "My network exercise" Exercise net:v1.0
docker run -ti --name web -p 80:80 net:v1.0 bash
```

> -p hostPort:containerPort 映射所有 IP 地址上的指定端口到容器内部
>
> -p ip:hostPort:containerPort 映射指定 IP 地址上的指定端口到容器内部
>
> -p ip::containerPort 映射指定 IP 地址上的任意端口到容器内部

### 容器互联

```bash
docker run -ti --name source net:v1.0 bash
docker run -ti --name receiver --link source:sender net:v1.0 bash
```

## Dockerfile

### 配置

```bash
FROM centos:6.9

RUN yum install vim -y
RUN yum install wget -y
```

### 创建镜像

```bash
docker build -t net:v1.2 .
```

从这个镜像运行一个容器

```bash
docker run --name sshtest --rm -d -p 6666:22 net:v1.2
```

> 这里的 - -rm 参数的作用是容器终止后立刻删除。

## Dockerfile 命令

### FROM

FROM 指令指定基础镜像，我们定制的镜像是在基础镜像之上进行修改。FROM 指令必须是 dockerfile 文件的第一条指令。

### LABEL

LABEL 指令添加元数据到一个镜像。LABEL 是键值对。还有一个指令 MAINTAINER 用来添加维护者信息，不过现在推荐使用 LABEL 而不是 MAINTAINER。

```bash
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

### COPY 复制文件

COPY <源路径> <目标路径> COPY [“<源路径>”，...，“<目标路径>”]

COPY 命令将上下文目录中的文件复制到镜像内的目标路径中，目标路径可以是绝对路径，也可以是由 WORKDIR 命令指定的相对路径（参见 WORKDIR 命令），目标路径无需事先创建，若不存在会自动创建。

```bash
COPY hom* /mydir/
COPY hom?.txt /mydir/
```

### ADD 高级的文件复制

ADD 命令格式和 COPY 完全一样，但是 ADD 命令会在复制的同时做一些额外的工作，比如，如果源路径是 URL，ADD 命令会下载文件再放到目标路径；如果是压缩文件，会解压后放到目标路径。由于这个命令的附加属性，推荐尽量使用 COPY，仅仅在需要自动解压缩或者下载的场合使用 ADD。

### RUN

RUN 是用来执行命令的，这条指令的格式有两种：

Shell 格式是“RUN <命令>”，我们的 Dockerfile 就是使用的 shell 格式； exec 格式：RUN [<"可执行文件">，<“参数1”>，<“参数2”>，... ]，和函数调用的格式很相似。

```bash
RUN apt-get install vim
RUN ["/bin/bash", "-c", "echo hello"]
```

### CMD

CMD 是容器启动命令。和 RUN 命令类似，它也有2种格式：

shell 格式： CMD <命令>

exec 格式： CMD [<"可执行文件">，<“参数1”>，<“参数2”>，... ]

记得我们早就说过，容器不是虚拟机，容器的本质是进程。既然是进程，就需要指定进程运行的时候的参数和程序。CMD 就是为容器主进程启动命令而存在的。

使用 CMD 命令的时候，初学者容易混淆前台运行和后台运行。再强调一遍，Docker 不是虚拟机，容器是进程，所以容器中的应用都应该以前台模式运行。

### ENTRYPOINT

ENTRYPOINT 命令和 CMD 一样有 shell 格式和 exec 格式，并且和 CMD 命令一样用来指定容器启动程序及参数。但是二者的适用场合有所不同。最重要的是，ENTRYPOINT 可以让我们把容器当成一条指令运行。下面，通过举例体会一下吧：

### ENV

ENV 命令用来设置环境变量，其它指令可以使用这些环境变量。

ENV key value
ENV key1=value2 key2=value2

```bash
ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffy

ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```

### VOLUME

VOLUME 命令用来定义匿名卷。

VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
我们已经知道，不应该向容器存储层写入数据，因该写到数据卷里面。为了防止用户在运行容器的时候忘记挂载数据卷，可以在 Dockerfile 里先定义匿名卷，这样即使忘记挂载数据卷，容器也不会向容器存储层写入大量数据。

```bash
VOLUME /data
```

### EXPOSE

EXPOSE 用来暴露端口，格式为：EXPOSE <端口1> [<端口2>……]

值得注意的是，EXPOSE 只是声明运行容器时提供的服务端口，这仅仅是一个声明，在运行容器的时候并不会因为这个声明就会开启端口服务，你依旧需要使用 -P 或者 -p 参数映射端口。在 Dockerfile 中写这样的端口声明有助于使用者理解这个镜像开放哪些服务端口，以便配置映射。并且，可以在 docker run 命令执行的时候使用 -P 参数随机映射宿主主机端口到 EXPOSE 的容器端口。

```bash
EXPOSE 22
```

### WORKDIR

WORKDIR <工作目录路径>

```bash
WORKDIR /mydir
RUN echo "Hello world." > test.txt
```

### USER

USER <用户名>
指定用户。这个命令会影响其后的命令的执行身份。当然，前提是你先创建用户。

```bash
RUN    groupadd    -r  zsc &&  useradd -r  -g  zsc zsc
USER    zsc
```

## 参考

* [docker](http://docker.com/)
* [docker-cn](http://docker-cn.com/)
* [notes-about-specifying-volumes](https://docs.docker.com/engine/reference/builder/#notes-about-specifying-volumes)
* [compose](https://docs.docker.com/compose/overview/)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
