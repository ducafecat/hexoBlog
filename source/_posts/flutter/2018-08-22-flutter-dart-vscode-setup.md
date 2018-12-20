---
title: Flutter 开发 - 开发环境搭建 VSCode IOS Android
top: false
date: 2018-08-22 14:47:23
tags: Flutter
categories: Flutter 开发
thumbnail:
---

# Flutter 开发 - 开发环境搭建 IOS Android

![ducafecat_2018-08-22-17-20-34](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-22-17-20-34.png)

本文系统 MacOS

## 配置 Flutter SDK

- 下载 SDK

```sh
https://flutter.io/sdk-archive/
https://storage.flutter-io.cn/flutter_infra/releases/beta/macos/flutter_macos_v0.5.1-beta.zip
```

国内用户请阅读 [Using Flutter in China](https://github.com/flutter/flutter/wiki/Using-Flutter-in-China) 替换镜像域名

解压到 `/app/flutter`

- 配置环境

```sh
vim ~/.bash_profile
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
export PATH=/app/flutter/bin:$PATH
source ~/.bash_profile
```

- 测试环境

```sh
flutter -h
flutter doctor
```

## 开发工具 Android Studio

- 安装 Android Studio

[Android Studio, 3.0 或更高版本.](https://developer.android.com/studio/index.html)

- 安装插件 `Preferences>Plugins` 搜索 `Flutter`

- 创建新应用 `File>New Flutter Project`

- 设置 SDK 安装目录

- 选择 `模拟器` `启动项目`

![ducafecat_2018-08-22-17-03-23](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-22-17-03-23.png)

- 启动完成

![ducafecat_2018-08-22-17-24-54](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-22-17-24-54.png)

## 开发工具 VSCode

- 安装 VS Code

[code.visualstudio.com](https://code.visualstudio.com/)

- 安装 `Flutter` `Dart` 插件

- 命令 `Flutter: Run Flutter Doctor` 验证您的设置

- 新建项目 `Flutter: New Project`

- 启动模拟器 `open -a Simulator`

- 启动调试 `F5`

![ducafecat_2018-08-22-16-01-04](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-08-22-16-01-04.png)

## 部署 ios 设备

- 安装插件

```sh
brew update
brew install --HEAD libimobiledevice
brew install ideviceinstaller ios-deploy cocoapods
pod setup
```

- 打开 `open ios/Runner.xcworkspace`

- 配置签名

- 运行设备

## 采坑

### 国内被墙问题严重

仔细阅读 [Using-Flutter-in-China](https://github.com/flutter/flutter/wiki/Using-Flutter-in-China)

- 各种 URL 域名替换

https://storage.flutter-io.cn/flutter_infra/releases/beta/macos/flutter_macos_v0.7.3-beta.zip

- 全局环境变量

```sh
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

## 参考

- [Flutter 官方](https://flutter.io/)
- [Flutter 中文官方](https://flutter-io.cn/)
- [Using-Flutter-in-China](https://github.com/flutter/flutter/wiki/Using-Flutter-in-China)
- [Flutter 中文网](https://flutterchina.club/)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
