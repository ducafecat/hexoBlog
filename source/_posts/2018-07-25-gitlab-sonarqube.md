---
title: gitlab-sonarqube
top: false
date: 2018-07-25 17:59:22
tags:
categories:
thumbnail:
---

# gitlab-sonarqube

## 启动 sonarqube

```bash
sonar:
    restart: always
    image: sonarqube:alpine
    volumes:
     - ./sonar/data:/opt/sonarqube/data
     - ./sonar/extensions:/opt/sonarqube/extensions
    ports:
     - "9000:9000"
```

## 手动提交检查 查看环境

- 下载 [sonar-scanner](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)

- 新建 `sonar-project.properties`

```bash
# must be unique in a given SonarQube instance
sonar.projectKey=hmadmin-eggjs-api
# this is the name and version displayed in the SonarQube UI. Was mandatory prior to SonarQube 6.1.
sonar.projectName=hmadmin eggjs api
sonar.projectVersion=1.0
 
# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
# This property is optional if sonar.modules is set. 
sonar.sources=.

sonar.host.url=http://localhost:9000

sonar.login=9ff97d583ca78076e932111d5776b706da745798

# sonar.analysis.mode=preview

# Encoding of the source code. Default is default system encoding
#sonar.sourceEncoding=UTF-8

```

- 执行提交

```bash
sonar-scanner
```

## 插件 sonar-gitlab-plugin

- [下载 sonar-gitlab-plugin](https://github.com/gabrie-allaigre/sonar-gitlab-plugin)



## 参考

- [Gitlab Ci With Sonarqube](https://www.slahser.com/2017/04/05/gitlab-ci-with-sonarqube/)
- [SonarQube 之 gitlab-plugin 配合 gitlab-ci 完成每次 commit 代码检测](https://cloud.tencent.com/developer/article/1010601)
- [Continuous Code Quality Analysis with SonarQube](https://medium.com/@niko.huber/continuous-code-quality-analysis-with-sonarqube-6a9146912b7d)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
