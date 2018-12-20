---
title: Spring Boot2 - 01 helloword
top: false
date: 2018-12-11 11:24:07
tags:
categories:
thumbnail:
---

# Spring Boot2 - 01 helloword

## 环境工具

- Java SE Development Kit 8

https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

- Maven

http://maven.apache.org/download.cgi

- IntelliJ IDEA

https://www.jetbrains.com/idea/download/

## 环境变量

```sh
vi ~/.bash_profile

export JAVA_HOME=`/usr/libexec/java_home -v 1.8`
export M2_HOME=/apache-maven
export PATH=$PATH:$M2_HOME/bin
```

## Maven 阿里镜像

```xml
  <mirrors>
    <!-- mirror
     | Specifies a repository mirror site to use instead of a given repository. The repository that
     | this mirror serves has an ID that matches the mirrorOf element of this mirror. IDs are used
     | for inheritance and direct lookup purposes, and must be unique across the set of mirrors.
     |
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>Human Readable Name for this Mirror.</name>
      <url>http://my.repository.com/repo/path</url>
    </mirror>
     -->
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>https://maven.aliyun.com/repository/public/</url>
      <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
```

## 编写 helloword

### 1. 使用 IDEA 构建

- 填写信息
  - Group，一般填写公司域名，比如百度公司就会填：com.baidu，演示使用 com.neo。
  - Artifact，可以理解为项目的名称了，可以根据实际情况来填，本次演示填 hello。
  - Dependencies，在这块添加我们项目所依赖的 Spring Boot 组件，可以多选，本次选择 Web、Devtools 两个模块。

### 2. 目录结构

- Spring Boot 的基础结构共三个文件，具体如下：

```sh
src/main/java：程序开发以及主程序入口；
src/main/resources：配置文件；
src/test/java：测试程序。
```

- 建议的目录结构
  - Application.java，建议放到根目录下面，是项目的启动类，Spring Boot 项目只能有一个 main() 方法；
  - comm 目录建议放置公共的类，如全局的配置文件、工具类等；
  - model 目录主要用于实体（Entity）与数据访问层（Repository）；
  - repository 层主要是数据库访问层代码；
  - service 层主要是业务类代码；
  - web 层负责页面访问控制。

```sh
myproject
 +-src
    +- main
         +- java
              +- com.example.myproject
                    +- comm
                    +- model
                    +- repository
                    +- service
                    +- web
                    +- Application.java
         +- resources
              +- static
              +- templates
              +- application.properties
    +- test
 +-pom.xml
```

- resources 目录下：
  - static 目录存放 web 访问的静态资源，如 js、css、图片等；
  - templates 目录存放页面模板；
  - application.properties 存放项目的配置信息。

### 3. Pom 包

- 第一部分为项目的描述信息

```xml
<groupId>com.neo</groupId>
<artifactId>hello</artifactId>
<version>2.0.5.RELEASE</version>
<packaging>jar</packaging>

<name>hello</name>
<description>Demo project for Spring Boot</description>

groupId，项目的包路径；
artifactId，项目名称；
version，项目版本号；
packaging，一般有两个值：jar、war，表示使用 Maven 打包时构建成 Jar 包还是 War 包；
name，项目名称；
description，项目描述。
```

- 第二部分为项目的依赖配置信息

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.5.RELEASE</version>
  <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

- 第三部分为构建时需要的公共变量

```xml
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
  <java.version>1.8</java.version>
</properties>
```

- 第四部分为构建配置

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```

### 4. 编写 Controller 内容

```sh
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello() {
        return "hello world";
    }

}
```

### 5. 手动编译运行

```sh
# 执行打包命令
mvn clean package
# 以 Jar 包的形式启动
java -jar target/hello-0.0.1-SNAPSHOT.jar
```

### 6. 热部署

- 配置 IDEA

选择 File | Settings | Compiler 命令，然后勾选 Build project automatically 复选框，低版本的 IDEA 请勾选 make project automatically 复选框。

- 使用快捷键 Command + Shift + A，在输入框中输入 Registry，勾选 compile.automake.allow.when.app.running 复选框

![](2018-12-11-14-34-24.png)

![](2018-12-11-14-36-17.png)

### 7. 单元测试

- 开始前准备数据

```java
@Before
public void setUP() throws Exception {
    mockMvc = MockMvcBuilders.standaloneSetup(new HelloController()).build();
}
```

- 返回测试数据

```java
@Test
public void hello1() throws Exception {
    mockMvc.perform(MockMvcRequestBuilders
            .post("/hello?name=hans")
            .accept(MediaType.APPLICATION_JSON_UTF8))
            .andDo(print());
}
```

- 断言

```java
@Test
public void hello2() throws Exception {
    mockMvc.perform(MockMvcRequestBuilders
            .post("/hello?name=hans")
            .accept(MediaType.APPLICATION_JSON_UTF8))
            .andExpect(MockMvcResultMatchers.content().string(Matchers.containsString("hans123")));
}
```

## 代码

## 参考

- [阿里镜像](http://maven.aliyun.com/mvn/view?spm=a2c4e.11153940.blogcont621196.11.3bd1b4f5CGqCQw)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
