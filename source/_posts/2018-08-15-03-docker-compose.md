---
title: Docker - 03 编排容器 Docker Compose 指令速查表
top: false
date: 2018-08-15 10:48:39
tags: docker compose
categories: Docker
thumbnail: http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-14-10-56-05.png
---

# Docker - 03 编排容器 Docker Compose 指令速查表

## 0 安装

- 下载

```bash
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```

- 加执行权限

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

## 1 docker-compose CLI 命令

### 1.1 主要

|  命令 | 说明 | 使用 |
| --- | --- | --- |
| up | 创建并运行作为服务的容器 | ![ducafecat_2018-08-16-11-42-05](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-42-05.png) |
| down | 停止服务容器并清除 | ![ducafecat_2018-08-16-11-42-33](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-42-33.png) |

### 1.2 其它

|  命令 | 说明 | 使用 |
| --- | --- | --- |
| bind | build需要的镜像 | build [options] [--build-arg key=val...] [SERVICE...] |
| config | 验证脚本 | config [options] |
| create | 创建镜像不启动 | create [options] [SERVICE...] |
| events | 监听容器事件 | events [options] [SERVICE...] |
| exec | 执行指定容器执行程序 | exec [options] [-e KEY=VAL...] SERVICE COMMAND [ARGS...] |
| run | 运行容器一次性的程序 | run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] [-l KEY=VALUE...] SERVICE [COMMAND] [ARGS...] |
| kill | 强行停止服务 | kill [options] [SERVICE...] |
| pause | 暂停服务 | pause [SERVICE...] |
| unpause | 恢复被暂停的服务 | unpause [SERVICE...] |
| stop | 停止运行一个服务的所有容器 | stop [options] [SERVICE...] |
| start | 启动运行某个服务的所有容器 | start [SERVICE...] |
| restart | 重启某个服务的所有容器 | restart [options] [SERVICE...] |
| rm | 删除停止的服务(容器) | rm [options] [SERVICE...] |
| logs | 展示service的日志 | logs [options] [SERVICE...] |
| top | 容器资源占用 | top [SERVICE...] |
| ps | 容器列表 | ps [options] [SERVICE...] |
| port | 查看服务中的端口被映射到了宿主机的哪个端口上 | port [options] SERVICE PRIVATE_PORT |
| pull | 拉取服务依赖的镜像 | pull [options] [SERVICE...] |
| push | 提交镜像 | push [options] [SERVICE...] |
| bundle | 打包 DAB 文件 | bundle [options] |
| scale | 指定某一个服务启动的容器的个数 | scale [SERVICE=NUM...] |

## 2 docker-compose 指令速查表

**按字母排列**

| 命令 | 说明 | 用法 |
| --- | --- | --- |
| build | 编译Dockerfile生成镜像 | ![ducafecat_2018-08-16-10-59-07](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-10-59-07.png) |
| command | 覆盖容器启动后默认执行的命令 | ![ducafecat_2018-08-16-10-59-36](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-10-59-36.png) |
| container_name | 容器的名字 | ![ducafecat_2018-08-16-10-59-52](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-10-59-52.png) |
| cap_add,cap_drop | 加入或者去掉容器能力 | ![ducafecat_2018-08-15-18-18-29](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-15-18-18-29.png) |
| depends_on | 容器的依赖 | ![ducafecat_2018-08-16-11-00-51](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-00-51.png) |
| configs | 导入配置 | ![ducafecat_2018-08-16-11-01-17](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-01-17.png) |
| dns | 设置DNS | ![ducafecat_2018-08-16-11-02-46](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-02-46.png) |
| dns_search | 自定义DNS搜索范围 | ![ducafecat_2018-08-16-11-04-10](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-04-10.png) |
| devices | 设备映射列表 | ![ducafecat_2018-08-16-11-04-29](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-04-29.png) |
| driver_opts | 给驱动传值 | ![ducafecat_2018-08-16-11-05-25](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-05-25.png) |
| entrypoint | 指定接入点 | ![ducafecat_2018-08-16-11-08-49](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-08-49.png) |
| env_file | 导入环境变量文件 | ![ducafecat_2018-08-16-11-09-15](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-09-15.png) |
| environment | 设置环境变量 | ![ducafecat_2018-08-16-11-09-34](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-09-34.png) |
| expose | 暴露的端口 | ![ducafecat_2018-08-16-11-09-50](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-09-50.png) |
| external_links | 连接单独启动的容器 | ![ducafecat_2018-08-16-11-10-07](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-10-07.png) |
| extra_hosts | 修改 /etc/hosts | ![ducafecat_2018-08-16-11-10-25](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-10-25.png) |
| healthcheck | 检查状态 | ![ducafecat_2018-08-16-11-10-57](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-10-57.png) |
| image | 镜像 | ![ducafecat_2018-08-16-11-13-55](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-13-55.png) |
| labels | 向容器添加元数据 | ![ducafecat_2018-08-16-11-14-20](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-14-20.png) |
| links | 连接容器 | ![ducafecat_2018-08-16-11-15-38](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-15-38.png) |
| logging | 配置日志服务 | ![ducafecat_2018-08-16-11-16-42](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-16-42.png) ![ducafecat_2018-08-16-11-16-53](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-16-53.png) |
| network_mode | 网络模式 | ![ducafecat_2018-08-16-11-17-10](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-17-10.png) |
| networks | 加入指定网络 | ![ducafecat_2018-08-16-11-17-24](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-17-24.png) |
| pid | 跟主机系统共享进程命名空间 | ![ducafecat_2018-08-16-11-33-03](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-33-03.png) |
| ports | 映射端口 | ![ducafecat_2018-08-16-11-18-48](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-18-48.png) ![ducafecat_2018-08-16-11-19-01](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-19-01.png) |
| restart | 出错重启方式 | ![ducafecat_2018-08-16-11-18-04](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-18-04.png) |
| stop_signal | 设置另一个信号来停止容器 | ![ducafecat_2018-08-16-11-30-54](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-30-54.png) |
| tmpfs | 挂载临时目录到容器内部 | ![ducafecat_2018-08-16-11-31-11](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-31-11.png) |
| volumes | 挂载一个目录 | ![ducafecat_2018-08-16-11-31-43](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-16-11-31-43.png) |

## 3 例子 - 运行容器服务 WordPress

- 编写 `docker-compose.yml`

```docker-compose.yml
version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
```

- 运行 `docker-compose up`

## 参考

- [docker-compose CLI](https://docs.docker.com/compose/reference/overview/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Get started with WordPress](https://docs.docker.com/compose/wordpress/)
- [Compose file version 3 reference](https://docs.docker.com/compose/compose-file/)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
