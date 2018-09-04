---
title: gitlab + sonarqube 集成代码审检平台
top: false
date: 2018-08-19 17:59:22
tags: gitlab sonarqube
categories: devOps
thumbnail:
---

# gitlab + sonarqube 集成代码审检平台

## 1 启动 sonarqube

编写 `docker-compose.yml` 文件

```docker-compose.yml
version: '3.3'

services:
   db:
     image: mysql:5.7.22
     restart: always
    #  ports:
    #    - 3306:3306
     volumes:
       - ./mysql/db:/var/lib/mysql
       - LANG=C.UTF-8
     environment:
       - MYSQL_ROOT_PASSWORD=sonar
       - MYSQL_DATABASE=sonar
       - MYSQL_USER=sonar
       - MYSQL_PASSWORD=sonar
     command: mysqld  --skip-ssl --character_set_server=utf8mb4 --sql_mode=""

   sonar:
      restart: always
      image: sonarqube:alpine
      depends_on:
       - db
      volumes:
       - ./sonar/data:/opt/sonarqube/data
       - ./sonar/extensions:/opt/sonarqube/extensions
      ports:
       - 9000:9000
       - 9092:9092
      environment:
       - SONARQUBE_JDBC_USERNAME=sonar
       - SONARQUBE_JDBC_PASSWORD=sonar
       - SONARQUBE_JDBC_URL=jdbc:mysql://db:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true
```

## 2 安装插件

**进入 配置 -> 应用市场**

- 汉化插件

![ducafecat_2018-08-17-15-43-58](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-43-58.png)

- GitLab 插件

![ducafecat_2018-08-17-15-44-23](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-44-23.png)

- GitLab 登录插件

![ducafecat_2018-08-17-15-46-47](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-15-46-47.png)

## 3 配置 Gitlab 集成

### 3.1 创建 gitlab 同步账号、设置访问令牌

![ducafecat_2018-08-17-16-07-45](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-16-07-45.png)

### 3.2 配置 sonar 插件

- 配置 gitlab 地址、访问令牌

![ducafecat_2018-08-17-16-09-44](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-16-09-44.png)

- 开启每次提交建议

![ducafecat_2018-08-17-16-10-47](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-16-10-47.png)

- 显示所有建议

![ducafecat_2018-08-17-16-11-18](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-16-11-18.png)

### 3.3 安装 sonar-scanner

[下载 sonar-scanner](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)

![ducafecat_2018-08-17-16-19-35](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-17-16-19-35.png)

下载后放到 `/usr/local/bin/sonar-scanner`

记得给可执行权限 `sudo chmod +x /usr/local/bin/sonar-scanner`

### 3.4 编写 sonar-project.properties 配置文件

```yml
sonar.host.url=http://sonar.xxx.xxx
sonar.login=b45c1cfb006d1d5c359dxxxxxxx867dfe0f5e91
sonar.sourceEncoding=UTF-8
sonar.modules=javascript-module
# sonar.modules=java-module,javascript-module,html-module,css-module

# sonar.sources=.
# sonar.tests=test
# sonar.language=js
# sonar.exclusions=test

# # Java module
# java-module.sonar.projectName=java-module
# java-module.sonar.language=java
# # .表示projectBaseDir指定的目录
# java-module.sonar.sources=.
# java-module.sonar.projectBaseDir=src
# sonar.binaries=classes

# JavaScript module
javascript-module.sonar.projectName=javascript-module
javascript-module.sonar.language=js
javascript-module.sonar.sources=.
javascript-module.sonar.projectBaseDir=app

# Html module
# html-module.sonar.projectName=html-module
# html-module.sonar.language=web
# html-module.sonar.sources=.
# html-module.sonar.projectBaseDir=web

# CSS module
# css-module.sonar.projectName=css-module
# css-module.sonar.language=css
# css-module.sonar.sources=.
# css-module.sonar.projectBaseDir=web
```

### 3.5 编写 .gitlab-ci.yml

```yml
stages:
  - analyze

sonar_analyze:
  stage: analyze
  script:
    - ci/sonar_analyze.sh
  tags:
    - analyze
```

### 3.6 编写 sonar_analyze.sh

```sh
#!/bin/bash

sonar-scanner \
    -Dsonar.analysis.mode=preview \
    -Dsonar.projectKey=gitlab:$CI_COMMIT_REF_NAME:$CI_PROJECT_NAME \
    -Dsonar.projectName=gitlab:$CI_COMMIT_REF_NAME:$CI_PROJECT_NAME \
    -Dsonar.projectVersion=1.0.$CI_PIPELINE_ID \
    -Dsonar.issuesReport.html.enable=true \
    -Dsonar.gitlab.project_id=$CI_PROJECT_ID \
    -Dsonar.gitlab.commit_sha=$CI_COMMIT_SHA \
    -Dsonar.gitlab.ref_name=$CI_COMMIT_REF_NAME

if [ $? -eq 0 ]; then
    echo "sonarqube code-publish over."
fi
```

## 参考

- [Gitlab Ci With Sonarqube](https://www.slahser.com/2017/04/05/gitlab-ci-with-sonarqube/)
- [SonarQube 之 gitlab-plugin 配合 gitlab-ci 完成每次 commit 代码检测](https://cloud.tencent.com/developer/article/1010601)
- [Continuous Code Quality Analysis with SonarQube](https://medium.com/@niko.huber/continuous-code-quality-analysis-with-sonarqube-6a9146912b7d)
- [CI variables](https://docs.gitlab.com/ce/ci/variables/)
- [SonarJS](https://docs.sonarqube.org/display/PLUG/SonarJS)
- [Sonar Properties Parameters](https://docs.sonarqube.org/display/SONAR/Analysis+Parameters)
- [sonar-l10n-zh](https://github.com/SonarQubeCommunity/sonar-l10n-zh)
- [sonar-auth-gitlab-plugin](https://github.com/gabrie-allaigre/sonar-auth-gitlab-plugin)
- [sonar-gitlab-plugin](https://github.com/gabrie-allaigre/sonar-gitlab-plugin)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
