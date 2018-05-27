---
title: React 快速上手 - 03 脚手架创建项目 一切都是组件
top: false
date: 2018-05-18 13:33:16
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 03 脚手架创建项目 一切都是组件

## 目标

* 通过脚手架创建项目
* 运行项目
* 了结项目结构
* 动手创建组件

## 创建项目

我推荐用脚手架作为开始，刚开始学习时一开始就捣腾 Webpack Babel 自己配置运行环境，然后你会发现一堆知识要补充，再然后就忘记了我一开始只是为了跑个 React。

本文讨论的都是基于 npm 构建的环境，如果直接在浏览器中运行，需要用 es5 直接写，否者会遇到兼容问题。

下面开始我们的正题！

### 1. 安装脚手架

```bash
cnpm install -g create-react-app
```

关于 `npm 加速` 问题，请查看上篇

### 2. 执行脚手架程序

```bash
cd ~/Documents/labs
create-react-app reactjs-example
```

我专门建了个 `labs` 目录，用来测试各种程序，这样不会把你的磁盘目录弄得很凌乱

录像

![create-react-app](http://oflimcy5e.bkt.clouddn.com/react-fast-03-create-react-app.gif)

### 3. 运行项目

```bash
cd reactjs-example
npm start
```

录像

![create-react-app-run](http://oflimcy5e.bkt.clouddn.com/react-fast-03-create-react-app-run.gif)

浏览器打开 `http://localhost:3000/`

![create-react-app-page](http://oflimcy5e.bkt.clouddn.com/react-fast-03-create-react-app-page.png)

看到这个干净的页面，大家可以放心了，环境一切正常

## 目录结构

```bash
.
├── README.md                                 | 帮助说明
├── build                                     | 发布目录
├── package.json                              | npm包管理
├── public                                    | 首页模板
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── src                                       | 源码
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   └── registerServiceWorker.js
```

我们大部分内容都是在 src 下完成

## 写个简单的作为开始

编辑 `app.js` 文件

```js
import React, {Component} from 'react'
const Element1 = () => <h2>组件1 - 常量</h2>
export default Element1
```

输出: `组件1 - 常量`

就3行，大家可以自己动手试下

## 能作为组件的js对象，大致如下几种:

### 1. 常量组件

```js
const Element1 = () => <h2>组件1 - 常量</h2>
```

### 2. 变量组件

```js
let Element2 = () => <h2>组件2 - 变量</h2>
```

### 3. es5 函数组件

```js
function Element3() {
  return <h2>组件3 - es5 函数</h2>
}
```

### 4. es6 箭头函数组件

```js
let Element4 = () => {
  return <h2>组件4 - es6 箭头函数</h2>
}
```

### 5. React.Component 类组件

```js
class Element5 extends Component {
  render() {
    return <h2>组件5 - React.Component 类对象</h2>
  }
}
```

## es6类 不是组件 **重要!**

```js
class Element6 {
  render() {
    return <h2>组件6 - es6 class 类对象</h2>
  }
}
```

运行后报错!

![std-class-not-as-component](http://oflimcy5e.bkt.clouddn.com/react-fast-03-class-component-err.png)

## 使用 codepen 调试代码

[codepen.io](https://codepen.io) 是一个不错的在线调试代码平台，我的 [codepen.io/ducafecat/](https://codepen.io/ducafecat/#)

本文代码 `codepen`

https://codepen.io/ducafecat/pen/WJzvRx

<p data-height="500" data-theme-id="0" data-slug-hash="WJzvRx" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 1 一切都是组件" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/WJzvRx/">React快速上手 1 一切都是组件</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## 项目编译

* 允许

```bash
cd reactjs-example
npm run build
```

执行成功后会生成 `/build` 目录，直接放到你的服务器上就能展示了~

录像

![create-react-app-build](http://oflimcy5e.bkt.clouddn.com/react-fast-03-create-react-app-build.gif)

* 如果本地打开 `build/index.html` ，你会发现找不到资源文件，因为默认是指向 `/` 修改本地允许

先打开模板看下

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    ...
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
```

这个 `%PUBLIC_URL%` 是全局变量

我们编译命令要这样写

```bash
- Windows
set PUBLIC_URL=./ && npm run build

- macOS
PUBLIC_URL=./ npm run build
```

再次打开 `build/index.html`

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  ...
  <link rel="manifest" href="./manifest.json">
  <link rel="shortcut icon" href="./favicon.ico">
  <title>React App</title>
  <link href="./static/css/main.65027555.css" rel="stylesheet">
</head>
```

替换完成！

## 本文代码

* [reactjs-example / 1-introducing.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/1-introducing.js)

## 参考文

* [Create React App](https://reactjs.org/docs/add-react-to-a-new-app.html)
* [Git create-react-app](https://github.com/facebook/create-react-app)
* [react-scripts](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
