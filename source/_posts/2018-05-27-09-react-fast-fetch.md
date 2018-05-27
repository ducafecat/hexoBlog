---
title: React 快速上手 - 09 数据请求 fetch
top: false
date: 2018-05-27 08:45:53
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 09 数据请求 fetch

本文重点在 `fetch` 基础使用、配置、实战，如果你很熟练了，可以直接 pass

## 目标

* 掌握 `fetch` 使用
  * 基础使用
  * 详细配置
  * 同步请求
  * 跨平台 cross-fetch

## 环境

* react 16.3.2
* fetch 内置
* cross-fetch 2.2.0

## 1 fetch 基础使用

### 1.1 数据结构

我在 `easy-mock` 上准备了模拟数据

https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/all

```json
[
  {
    "id": "610000200102261253",
    "name": "薛洋"
  },
  {
    "id": "350000199603020600",
    "name": "许磊"
  },
  {
    "id": "310000198105081812",
    "name": "崔娟"
  },
  {
    "id": "820000197101010860",
    "name": "石霞"
  }
]
```

一个列表，两个字段 `id` `name`

### 1.2 读取

```js
  fetch(
    'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/all'
  )
    .then(res => res.json())
    .then(data => {
      console.log(data)
      this.setState({users: data})
    })
    .catch(e => console.log('错误:', e))
```

`fetch` 是浏览器内置对象，所以我们不用安装包，直接使用

* 使用流程
  * fetch ...
  * then => res.json()
  * then => data

> 注意需要执行一次 `res.json()` 方法才能获取数据

### 1.3 打印

我们获取数据后，设置 `state` ，然后正常数据 `render` 就行，完整代码

```js
import React, {Component} from 'react'

class RequestView extends Component {
  constructor(props) {
    super(props)
    this.state = {users: []}
    this.handleClick = this.handleClick.bind(this)
  }
  handleClick() {
    fetch(
      'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/all'
    )
      .then(res => res.json())
      .then(data => {
        console.log(data)
        this.setState({users: data})
      })
      .catch(e => console.log('错误:', e))
  }

  render() {
    return (
      <div>
        <input type="button" value="点击 http-get 方式获取数据" onClickCapture={this.handleClick} />
        <ul>
          {this.state.users &&
            this.state.users.map((item, index) => (
              <li key={index.toString()}>{item.name}</li>
            ))}
        </ul>
      </div>
    )
  }
}
```

打印

![fetch](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-14-26-28.png)

* codepen

https://codepen.io/ducafecat/pen/bMxZEy

## 2 fetch 配置

我们来好好的看下这个 `fetch()` 全局方法

![fetch-init-1](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-14-49-21.png)
![fetch-init-2](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-59-13.png)

我举两个自定义例子，大家体会下

### 2.1 form 提交

#### 2.1.1 定义 `Headers` 请求头协议说明

```js
let initHeaders = new Headers()
initHeaders.append('Accept', 'application/json, text/plain, */*')
initHeaders.append('Cache-Control', 'no-cache')
initHeaders.append('Content-Type', 'application/x-www-form-urlencoded')
```

* 因为是表单，所以设置 `application/x-www-form-urlencoded`

#### 2.1.2 定义 `init` 配置

```js
let data = {uid: 1011}
let body = `uid=${data.uid}`

const init = {
  method: 'POST',
  credentials: 'include', // cookies
  cache: 'no-cache ', // cookies
  headers: initHeaders,
  body
}
```

* `method` 指定 `POST` 方式
* `credentials: 'include'` 表示每次都带上 `cookies`
* `headers` 请求头协议说明
* `body` 数据，格式 `key=val&key=val&key=val`

#### 2.1.3 请求提交

```js
fetch(
  'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/login',
  init
)
  .then(res => res.json())
  .then(data => {
    this.setState({user: data})
  })
  .catch(e => console.log('错误:', e))
```

这里就类似我们第一个基础例子了

* codepen

https://codepen.io/ducafecat/pen/QrVoKW

### 2.2 raw 提交

#### 2.2.1 定义 `Headers`

```js
let initHeaders = new Headers()
initHeaders.append('Accept', 'application/json, text/plain, */*')
initHeaders.append('Cache-Control', 'no-cache')
initHeaders.append('Content-Type', 'application/json;charset=UTF-8')
```

* `Content-Type` 类型需要定义成 `application/json;charset=UTF-8`

#### 2.2.2 定义 `init`

```js
let data = {uid: 1011}
let body = JSON.stringify(data, null, 2)

const init = {
  method: 'POST',
  credentials: 'include', // cookies
  cache: 'no-cache ', // cookies
  headers: initHeaders,
  body
}
```

* `json` 数据需要格式化 `JSON.stringify(data, null, 2)`

#### 2.2.3 请求提交

```js
fetch(
  'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/login',
  init
)
  .then(res => res.json())
  .then(data => {
    this.setState({user: data})
  })
  .catch(e => console.log('错误:', e))
```

* codepen

https://codepen.io/ducafecat/pen/gzdELZ

### 2.3 完整例子

代码

```js
import React, {Component} from 'react'

class RequestView extends Component {
  constructor(props) {
    super(props)
    this.state = {user: null}
    this.handlePostForm = this.handlePostForm.bind(this)
    this.handlePostJSON = this.handlePostJSON.bind(this)
  }
  handlePostForm() {
    let initHeaders = new Headers()
    initHeaders.append('Accept', 'application/json, text/plain, */*')
    initHeaders.append('Cache-Control', 'no-cache')
    initHeaders.append('Content-Type', 'application/x-www-form-urlencoded')

    let data = {uid: 1011}
    let body = `uid=${data.uid}`

    const init = {
      method: 'POST',
      credentials: 'include', // cookies
      cache: 'no-cache ', // cookies
      headers: initHeaders,
      body
    }
    fetch(
      'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/login',
      init
    )
      .then(res => res.json())
      .then(data => {
        this.setState({user: data})
      })
      .catch(e => console.log('错误:', e))
  }
  handlePostJSON() {
    let initHeaders = new Headers()
    initHeaders.append('Accept', 'application/json, text/plain, */*')
    initHeaders.append('Cache-Control', 'no-cache')
    initHeaders.append('Content-Type', 'application/json;charset=UTF-8')

    let data = {uid: 1011}
    let body = JSON.stringify(data, null, 2)

    const init = {
      method: 'POST',
      credentials: 'include', // cookies
      cache: 'no-cache ', // cookies
      headers: initHeaders,
      body
    }
    fetch(
      'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/login',
      init
    )
      .then(res => res.json())
      .then(data => {
        this.setState({user: data})
      })
      .catch(e => console.log('错误:', e))
  }

  render() {
    return (
      <div>
        <input
          type="button"
          value="点击 http-post form 表单"
          onClickCapture={this.handlePostForm}
        />
        <br />
        <input
          type="button"
          value="点击 http-post json raw 格式"
          onClickCapture={this.handlePostJSON}
        />
        {this.state.user && (
          <ul>
            <li>ID: {this.state.user.id}</li>
            <li>Name: {this.state.user.name}</li>
          </ul>
        )}
      </div>
    )
  }
}

export default RequestView

```

动图效果

![fetch-form-raw](http://oflimcy5e.bkt.clouddn.com/react-fetch2.gif)

## 3 fetch 同步 `async / wait`

### 3.1 同步写法

```js
async handleClick() {
  try {
    const res = await fetch(
      'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/all'
    )
    const users = await res.json()
    this.setState({users})
  } catch (error) {
    console.log('错误', error)
  }
}
```

* 函数本身要定义成 `async`
* `res.json()` 这个方法不要忘记调用
* 异常处理要用 `try ... catch ...`

### 3.2 完成例子

代码

```js
import React, {Component} from 'react'

class RequestView extends Component {
  constructor(props) {
    super(props)
    this.state = {users: []}
    this.handleClick = this.handleClick.bind(this)
  }
  async handleClick() {
    try {
      const res = await fetch(
        'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/all'
      )
      const users = await res.json()
      this.setState({users})
    } catch (error) {
      console.log('错误', error)
    }
  }

  render() {
    return (
      <div>
        <input
          type="button"
          value="点击 async / await 方式获取数据"
          onClickCapture={this.handleClick}
        />
        <ul>
          {this.state.users &&
            this.state.users.map((item, index) => (
              <li key={index.toString()}>{item.name}</li>
            ))}
        </ul>
      </div>
    )
  }
}

export default RequestView

```

打印

![fetch-await](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-05-00.png)

## 4 fetch 兼容性

好像 `fetch` 很强啊，不用安装包，全局方法直接用，但是有一个小问题，对浏览器的依赖，先看下 [caniuse](https://caniuse.com/) 平台的报告:

![caniuse-fetch](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-10-25.png)

`IE` 全阵亡，低版本 `Safari` 兼容问题，`Firefox` `Chrome` `Opera` 如果特性不开的话也会出问题，懂的同学说可以浏览器配置和打 `polyfill` 补丁，但是这样需要自己做很多工作，如果你的代码需要跑到 `node` 端呢（因为`API 业务层`很有可能复用性很高）。

如果考虑兼容性，所以我们还是用第三方组件

## 5 cross-fetch 组件

### 5.1 介绍

[cross-fetch](https://github.com/lquixada/cross-fetch)

![cross-fetch](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-29-07.png)

感觉 `Star` 不是很多么。。。

接着往下看

### 5.2 多平台支持

README 里说了各种平台支持、知名项目也在用

是真的么。。。

接着往下看

![Supported environments](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-32-22.png)

### 5.3 包研究

打开文件 `package.json`

![dependencies](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-43-12.png)

来看下这两个包

* node-fetch

https://github.com/bitinn/node-fetch

![node-fetch](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-44-03.png)

* whatwg-fetch

![ducafecat_2018-05-27-16-08-31](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-16-08-31.png)

指向了 [github/fetch](https://github.com/github/fetch)

* github/fetch

https://github.com/github/fetch

![github-fetch](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-44-37.png)

好多 Star 看着就放心，大家用吧

### 5.4 例子

* 安装

```bash
yarn add cross-fetch
```

* 代码

```js
import React, {Component} from 'react'
import fetch from 'cross-fetch'

class RequestView extends Component {
  constructor(props) {
    super(props)
    this.state = {users: []}
    this.handleClick = this.handleClick.bind(this)
  }
  async handleClick() {
    try {
      const res = await fetch(
        'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example/user/all'
      )
      const users = await res.json()
      this.setState({users})
    } catch (error) {
      console.log('错误', error)
    }
  }

  render() {
    return (
      <div>
        <input
          type="button"
          value="点击 cross-fetch 组件方式 获取数据"
          onClickCapture={this.handleClick}
        />
        <ul>
          {this.state.users &&
            this.state.users.map((item, index) => (
              <li key={index.toString()}>{item.name}</li>
            ))}
        </ul>
      </div>
    )
  }
}

export default RequestView
```

* 打印

![Cross-Fetch](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-27-15-38-30.png)

## 代码

* [7-1-requestFetch.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/7-1-requestFetch.js)
* [7-2-requestFetchConfig.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/7-2-requestFetchConfig.js)
* [7-3-requestFetchAwait.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/7-3-requestFetchAwait.js)
* [7-4-requestCrossFetch.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/7-4-requestCrossFetch.js)

## 参考

* [fetch](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)
* [axios](https://github.com/axios/axios)
* [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
* [cross-fetch](https://github.com/lquixada/cross-fetch)
* [兼容性 fetch](https://caniuse.com/#search=fetch)
* [Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
* [polyfill-fetch](https://github.com/github/fetch)
* [node-fetch](https://github.com/bitinn/node-fetch)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
