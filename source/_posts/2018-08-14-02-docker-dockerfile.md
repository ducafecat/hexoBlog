---
title: Docker 手册 - 02 创建镜像 Dockerfile 指令速查表
top: false
date: 2018-08-14 10:48:39
tags: dockerfile
categories: Docker
thumbnail: http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-14-10-56-05.png
---

# Docker 手册 - 02 创建镜像 Dockerfile 指令速查表

## docker build 命令

参数 | 说明 | 示例
----|------|--------
-f    | 指定 Dockerfile 位置         | docker build -f /path/to/a/Dockerfile .
-t    | 指定镜像名称                  | docker build -t shykes/myapp:v1.1.2 .
-t -t | 生成多个镜像                  | docker build -t shykes/myapp:v1.1.2 -t shykes/myapp:latest .

## Dockerfile 指令速查表

命令 | 说明 | 示例
----|------|--------
FROM       | 基于这个Image开始          | FROM nginx:latest
ENV        | 环境变量                  | ENV localfile /usr/local/nginx
RUN        | 新层中执行命令             | RUN /bin/bash -c 'source \$HOME/.bashrc; echo \$HOME'
LABEL      | 设置metadata             | LABEL version="1.0"
MAINTAINER | 维护者 (deprecated)      | MAINTAINER ducafecat
EXPOSE     | 声明容器监听端口           | EXPOSE 80 443
ADD        | 复制文件                  | ADD ./dist ${foo}/html
COPY       | 复制文件                  | COPY ./dist ${foo}/html
ENTRYPOINT | 容器启动时执行指令         | CMD ["ls"]
CMD        | 容器启动时执行指令默认值    | CMD ["-la"]
VOLUME     | 挂载点                   | VOLUME ["/data"]
USER       | 指定操作用户              | USER www
WORKDIR    | 设置工作目录              | WORKDIR /path/to/workdir
ARG        | 设置参数                 | ARG user=www
ONBUILD    | 镜像被From时触发          | ONBUILD RUN /bin/bash -c 'echo ONBUILD ...'
STOPSIGNAL | 停止信号退出              | STOPSIGNAL SIGTERM
HEALTHCHECK| 检查容器的健康状况         | HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
SHELL      | 覆盖默认shell            | SHELL ["powershell", "-command"]

## .dockerignore 文件

参数 | 说明
----|------
| #comment    | 忽略
| \*/temp*     | 纯文件/somedir/temporary.txt被排除，目录/somedir /temp也是如此。
| \*/\*/temp*   | /somedir/subdir/temporary.txt被排除。
| temp?       | /tempa和/tempb被排除在外。
| !README.md  | README.md 文件不会被排除

## 例子 - nginx 镜像

```Dockerfile
FROM debian:stretch-slim

LABEL maintainer="NGINX Docker Maintainers <docker-maint@nginx.com>"

ENV NGINX_VERSION 1.15.2-1~stretch
ENV NJS_VERSION   1.15.2.0.2.2-1~stretch

RUN set -x \
	&& apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y gnupg1 apt-transport-https ca-certificates \
	&& \
	NGINX_GPGKEY=573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62; \
	found=''; \
	for server in \
		ha.pool.sks-keyservers.net \
		hkp://keyserver.ubuntu.com:80 \
		hkp://p80.pool.sks-keyservers.net:80 \
		pgp.mit.edu \
	; do \
		echo "Fetching GPG key $NGINX_GPGKEY from $server"; \
		apt-key adv --keyserver "$server" --keyserver-options timeout=10 --recv-keys "$NGINX_GPGKEY" && found=yes && break; \
	done; \
	test -z "$found" && echo >&2 "error: failed to fetch GPG key $NGINX_GPGKEY" && exit 1; \
	apt-get remove --purge --auto-remove -y gnupg1 && rm -rf /var/lib/apt/lists/* \
	&& dpkgArch="$(dpkg --print-architecture)" \
	&& nginxPackages=" \
		nginx=${NGINX_VERSION} \
		nginx-module-xslt=${NGINX_VERSION} \
		nginx-module-geoip=${NGINX_VERSION} \
		nginx-module-image-filter=${NGINX_VERSION} \
		nginx-module-njs=${NJS_VERSION} \
	" \
	&& case "$dpkgArch" in \
		amd64|i386) \
# arches officialy built by upstream
			echo "deb https://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list.d/nginx.list \
			&& apt-get update \
			;; \
		*) \
# we're on an architecture upstream doesn't officially build for
# let's build binaries from the published source packages
			echo "deb-src https://nginx.org/packages/mainline/debian/ stretch nginx" >> /etc/apt/sources.list.d/nginx.list \
			\
# new directory for storing sources and .deb files
			&& tempDir="$(mktemp -d)" \
			&& chmod 777 "$tempDir" \
# (777 to ensure APT's "_apt" user can access it too)
			\
# save list of currently-installed packages so build dependencies can be cleanly removed later
			&& savedAptMark="$(apt-mark showmanual)" \
			\
# build .deb files from upstream's source packages (which are verified by apt-get)
			&& apt-get update \
			&& apt-get build-dep -y $nginxPackages \
			&& ( \
				cd "$tempDir" \
				&& DEB_BUILD_OPTIONS="nocheck parallel=$(nproc)" \
					apt-get source --compile $nginxPackages \
			) \
# we don't remove APT lists here because they get re-downloaded and removed later
			\
# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
# (which is done after we install the built packages so we don't have to redownload any overlapping dependencies)
			&& apt-mark showmanual | xargs apt-mark auto > /dev/null \
			&& { [ -z "$savedAptMark" ] || apt-mark manual $savedAptMark; } \
			\
# create a temporary local APT repo to install from (so that dependency resolution can be handled by APT, as it should be)
			&& ls -lAFh "$tempDir" \
			&& ( cd "$tempDir" && dpkg-scanpackages . > Packages ) \
			&& grep '^Package: ' "$tempDir/Packages" \
			&& echo "deb [ trusted=yes ] file://$tempDir ./" > /etc/apt/sources.list.d/temp.list \
# work around the following APT issue by using "Acquire::GzipIndexes=false" (overriding "/etc/apt/apt.conf.d/docker-gzip-indexes")
#   Could not open file /var/lib/apt/lists/partial/_tmp_tmp.ODWljpQfkE_._Packages - open (13: Permission denied)
#   ...
#   E: Failed to fetch store:/var/lib/apt/lists/partial/_tmp_tmp.ODWljpQfkE_._Packages  Could not open file /var/lib/apt/lists/partial/_tmp_tmp.ODWljpQfkE_._Packages - open (13: Permission denied)
			&& apt-get -o Acquire::GzipIndexes=false update \
			;; \
	esac \
	\
	&& apt-get install --no-install-recommends --no-install-suggests -y \
						$nginxPackages \
						gettext-base \
	&& apt-get remove --purge --auto-remove -y apt-transport-https ca-certificates && rm -rf /var/lib/apt/lists/* /etc/apt/sources.list.d/nginx.list \
	\
# if we have leftovers from building, let's purge them (including extra, unnecessary build deps)
	&& if [ -n "$tempDir" ]; then \
		apt-get purge -y --auto-remove \
		&& rm -rf "$tempDir" /etc/apt/sources.list.d/temp.list; \
	fi

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
```

## 例子 - redis 镜像

```Dockerfile
FROM debian:stretch-slim

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN groupadd -r redis && useradd -r -g redis redis

# grab gosu for easy step-down from root
# https://github.com/tianon/gosu/releases
ENV GOSU_VERSION 1.10
RUN set -ex; \
	\
	fetchDeps=" \
		ca-certificates \
		dirmngr \
		gnupg \
		wget \
	"; \
	apt-get update; \
	apt-get install -y --no-install-recommends $fetchDeps; \
	rm -rf /var/lib/apt/lists/*; \
	\
	dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	gpgconf --kill all; \
	rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc; \
	chmod +x /usr/local/bin/gosu; \
	gosu nobody true; \
	\
	apt-get purge -y --auto-remove $fetchDeps

ENV REDIS_VERSION 5.0-rc4
ENV REDIS_DOWNLOAD_URL http://download.redis.io/releases/redis-5.0-rc4.tar.gz
ENV REDIS_DOWNLOAD_SHA bfc7a27d3ba990e154e5b56484061f01962d40b7c77b520ed7a940914b267cec

# for redis-sentinel see: http://redis.io/topics/sentinel
RUN set -ex; \
	\
	buildDeps=' \
		ca-certificates \
		wget \
		\
		gcc \
		libc6-dev \
		make \
	'; \
	apt-get update; \
	apt-get install -y $buildDeps --no-install-recommends; \
	rm -rf /var/lib/apt/lists/*; \
	\
	wget -O redis.tar.gz "$REDIS_DOWNLOAD_URL"; \
	echo "$REDIS_DOWNLOAD_SHA *redis.tar.gz" | sha256sum -c -; \
	mkdir -p /usr/src/redis; \
	tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1; \
	rm redis.tar.gz; \
	\
# disable Redis protected mode [1] as it is unnecessary in context of Docker
# (ports are not automatically exposed when running inside Docker, but rather explicitly by specifying -p / -P)
# [1]: https://github.com/antirez/redis/commit/edd4d555df57dc84265fdfb4ef59a4678832f6da
	grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 1$' /usr/src/redis/src/server.h; \
	sed -ri 's!^(#define CONFIG_DEFAULT_PROTECTED_MODE) 1$!\1 0!' /usr/src/redis/src/server.h; \
	grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 0$' /usr/src/redis/src/server.h; \
# for future reference, we modify this directly in the source instead of just supplying a default configuration flag because apparently "if you specify any argument to redis-server, [it assumes] you are going to specify everything"
# see also https://github.com/docker-library/redis/issues/4#issuecomment-50780840
# (more exactly, this makes sure the default behavior of "save on SIGTERM" stays functional by default)
	\
	make -C /usr/src/redis -j "$(nproc)"; \
	make -C /usr/src/redis install; \
	\
	rm -r /usr/src/redis; \
	\
	apt-get purge -y --auto-remove $buildDeps

RUN mkdir /data && chown redis:redis /data
VOLUME /data
WORKDIR /data

COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 6379
CMD ["redis-server"]
```

## 参考

- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
- [Nginx Dockerfile](https://github.com/nginxinc/docker-nginx/blob/ddbbbdf9c410d105f82aa1b4dbf05c0021c84fd6/mainline/stretch/Dockerfile)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
