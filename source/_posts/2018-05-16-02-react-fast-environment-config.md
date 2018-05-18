---
title: React 快速上手 - 02 开发环境搭建
top: false
date: 2018-05-16 15:37:06
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 02 开发环境搭建

## 目标

* 安装 git 软件
* 安装 node npm 环境
* 配置 node npm 常用工具
* 安装 vscode 作为 IDE 开发工具
* 安装 vscode 常用开发插件 为 react 开发做准备

## 安装 git

这个必要性，就不多说了，进入开源的世界，没有 git 寸步难行

进入 [官网](https://git-scm.com)

![git](http://oflimcy5e.bkt.clouddn.com/react-fast-02-git.png)

下载后是一个安装包，一路 `下一步` 安装直到完成

## 安装 node npm

![nodejs](http://oflimcy5e.bkt.clouddn.com/react-fast-02-node.png)

打开网站 [https://nodejs.org/en/](https://nodejs.org/en/) 选择下载 LTS 版本。

下载后是一个安装包，一路 `下一步` 安装直到完成

测试安装结果

```bash
node -v
npm -v
```

## 使用淘宝镜像

为了加速 npm 的安装速度，我们用淘宝提供的源，这个问题在其它包管理软件也会遇到

```bash
npm config set registry https://registry.npm.taobao.org
```

验证配置

```bash
npm config get registry
```

这样就完成了加速

## 安装 cnpm

其实现在的 cnpm 版本已经很稳定了，早先会有下载包错误问题，毕竟产品成熟需要时间，[cnpm官网](http://npm.taobao.org)

全局安装

```bash
npm install -g cnpm
如果你没有配置淘宝镜像可以参数传入加速
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 安装 yarn

![yarn](http://oflimcy5e.bkt.clouddn.com/react-fast-02-yarn.png)

yarn 是个很优秀的包管理程序， react 官方示例都是推荐 yarn 安装，特点就是快，相信不久 npm 也会赶上的，比如已经下载过的包本地做缓存，下次就秒安装了

[yarn官网](https://yarnpkg.com)

### mac 安装

homebrew 方式

```bash
brew install yarn
```

如果没有安装 [homebrew](https://brew.sh)

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### windows 安装

直接下载安装包 [Yarn Setup](https://yarnpkg.com/latest.msi)

一路 `下一步` 安装直到完成

## 安装 nrm

![nrm -- NPM registry manager](http://oflimcy5e.bkt.clouddn.com/react-fast-02-nrm.png)

包源管理工具

* 安装

```bash
cnpm install -g nrm
```

* 显示可用源

```bash
> nrm ls

npm ---- https://registry.npmjs.org/
cnpm --- http://r.cnpmjs.org/
taobao - https://registry.npm.taobao.org/
nj ----- https://registry.nodejitsu.com/
rednpm - http://registry.mirror.cqupt.edu.cn/
npmMirror  https://skimdb.npmjs.com/registry/
edunpm - http://registry.enpmjs.org/
```

* 切换源

```bash
> nrm use npm

  verb config Skipping project config: /Users/hans/.npmrc. (matches userconfig)

  Registry has been set to: https://registry.npmjs.org/
```

个人喜欢 npm 用官方源，cnpm 用来安装淘宝镜像，这样遇到包问题，可以手动调整，比较灵活。

## 安装 n

![n – Interactively Manage Your Node.js Versions](http://oflimcy5e.bkt.clouddn.com/react-fast-02-n.png)

node 版本切换工具

* 安装

```bash
cnpm install -g n
```

* 本地版本列表

```bash
> n

  node/8.8.1
  node/8.9.4
  node/8.10.0
  node/8.11.1
  node/9.4.0
  node/9.9.0
```

* 安装、切换 版本

```bash
> n 8.11.2
```

* 切换 最新版本

```bash
> n latest
```

* 切换 最新稳定版本

```bash
> n stable
```

* 切换 长期支持版本

```bash
> n lts
```

* 删除本地版本

```bash
> n rm 8.8.1
```

## 安装 vscode

![vscode](http://oflimcy5e.bkt.clouddn.com/react-fast-02-vscode.png)

推荐理由 微软出品、免费、开源、速度快、轻量级、程序稳定、不卡、不卡、大文件秒开、语法高亮、升级频繁、配置方便

下载后是一个安装包，一路 `下一步` 安装直到完成

## 配置 vscode 格式文件 `.editorconfig`

```js
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
tab_width = 1
```

没有请创建文件

## 安装 vscode 插件 eslint

* 全局安装

```bash
cnpm install -g eslint
```

* 我们后面用 `脚手架` 创建项目，默认生成有 `.eslintrc.js` 配置文件

* 安装 vscode 插件, `侧栏 > 扩展 > 搜索 eslint`

![eslint](http://oflimcy5e.bkt.clouddn.com/react-fast-02-eslint.png)

* 配置 `vscode` , 打开菜单 `文件 > 首选项 > 设置`

```js
  "eslint.alwaysShowStatus": true,
  "eslint.autoFixOnSave": "off",
  "eslint.validate": [
    "javascript",
    {
      "language": "html",
      "autoFix": true
    },
    {
      "language": "vue",
      "autoFix": true
    },
    "javascriptreact",
    "html",
    "vue"
  ],
  "eslint.options": { "plugins": ["html"] },
```

## 安装 vscode 插件 prettier - Code formatter

代码格式化插件

* 安装 vscode 插件, `侧栏 > 扩展 > 搜索 prettier`

![prettier](http://oflimcy5e.bkt.clouddn.com/react-fast-02-prettier.png)

* 配置 `vscode` , 打开菜单 `文件 > 首选项 > 设置`

```js
  "prettier.singleQuote": true,
  "prettier.semi": false,
  "prettier.bracketSpacing": false,
  "prettier.useTabs": false,
  "prettier.tabWidth": 2,
```

* 使用，鼠标右键点击 `格式化文件`

![prettier-format-file](http://oflimcy5e.bkt.clouddn.com/react-fast-02-prettier-format.gif)

## 安装 vscode 插件 reactjs code snippets

代码片段工具

* 安装 vscode 插件, `侧栏 > 扩展 > 搜索 reactjs code snippets`

![reactjs code snippets](http://oflimcy5e.bkt.clouddn.com/react-fast-02-reactjs-snippets.png)

* 使用，新建文件 `MyApp.js`

![reactjs-snippets-rcc](http://oflimcy5e.bkt.clouddn.com/react-fast-02-reactjs-snippets-rcc.gif)

## 安装 vscode 插件 Auto Close Tag

html 标签自动补完插件

* 安装 vscode 插件, `侧栏 > 扩展 > 搜索 Auto Close Tag`

## 安装 vscode 插件 Auto Rename Tag

html 标签改名自动同步插件

* 安装 vscode 插件, `侧栏 > 扩展 > 搜索 Auto Rename Tag`

## 安装 vscode 插件 Debugger for Chrome

vscode chrome 调试工具

* 安装 vscode 插件, `侧栏 > 扩展 > 搜索 Debugger for Chrome`

![Debugger for Chrome](http://oflimcy5e.bkt.clouddn.com/react-fast-02-debugger.png)

* 调试配置文件 `launch.json`

```js
{
  // 使用 IntelliSense 了解相关属性。
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Chrome",
      "type": "chrome",
      "request": "launch",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceRoot}/src",
      "userDataDir": "${workspaceRoot}/.vscode/chrome",
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*"
      }
    }
  ]
}
```

* 使用录像

![use-debugger-for-chrome](http://oflimcy5e.bkt.clouddn.com/react-fast-02-debugger-for-chrome.gif)

> `http://localhost:3000` 服务需要已开启
>
> 安装完插件都需要重启 `vscode` 才能生效
