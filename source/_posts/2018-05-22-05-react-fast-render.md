---
title: React 快速上手 - 05 组件渲染控制
top: false
date: 2018-05-22 08:52:55
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 05 组件渲染控制

本质上讲，本章还是说 `JSX` ，作为一个组件关键是渲染，所以我作为一个重点，单独写一章，方便大家理解上有个重点。

理论上来说，渲染分两种方式 `JSX` `React.createElement` （我在本文最后会稍微点到下） ，如何灵活使用大家自己权衡，存在必有理由。

## 目标

掌握常见渲染控制

* 并列
* 条件判断
* 三目运算
* 循环
* 嵌套循环
* 空组件
* ref引用

## 渲染控制

### 1. 并列

组件嵌套并行，这是非常常见的情况，特别是容器组件，后面会细说

代码

```js
function UserView(props) {
  return <p>用户 xxx 你好，欢迎回来！</p>
}
function GuestView(props) {
  return <p>游客你好， 注册 | 登陆。</p>
}

function RenderView() {
  return (
    <div>
      <UserView />
      <GuestView />
    </div>
  )
}
```

输出

![jax-arrange](http://oflimcy5e.bkt.clouddn.com/react-fast-05-arrange.png)

codepen

https://codepen.io/ducafecat/pen/MGBzMQ

<p data-height="265" data-theme-id="0" data-slug-hash="MGBzMQ" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.1 渲染Render 并列" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/MGBzMQ/">React快速上手 3.1 渲染Render 并列</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 2. 条件判断

代码

```js
// 显示组件
function UserView(props) {
  return <p>用户 xxx 你好，欢迎回来！</p>
}
function GuestView(props) {
  return <p>游客你好， 注册 | 登陆。</p>
}

// 条件判断
function ConditionView(props) {
  return <div>{props.isLogin && <UserView />}</div>
}

// 容器
function RenderView() {
  return (
    <div>
      <ConditionView isLogin />
    </div>
  )
}
```

> 条件控制中间用 `&&` 符号链接

大家可以动手改下看看

```js
  <ConditionView isLogin={false} />
```

输出

![jsx-condition](http://oflimcy5e.bkt.clouddn.com/react-fast-05-condition.png)

codepen

https://codepen.io/ducafecat/pen/rvrQXK

<p data-height="265" data-theme-id="0" data-slug-hash="rvrQXK" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.2 渲染Render 条件判断" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/rvrQXK/">React快速上手 3.2 渲染Render 条件判断</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 3. 三目运算

代码

```js
// 显示组件
function UserView(props) {
  return <p>用户 xxx 你好，欢迎回来！</p>
}
function GuestView(props) {
  return <p>游客你好， 注册 | 登陆。</p>
}

// 三目运算
function InlineIfElseView(props) {
  return <div>{props.isLogin === true ? <UserView /> : <GuestView />}</div>
}

// 容器
function RenderView() {
  return (
    <div>
      <InlineIfElseView isLogin={false} />
    </div>
  )
}
```

> `三目运算` 在条件控制里, 还是很实用的

输出

![jsx-InlineIfElse](http://oflimcy5e.bkt.clouddn.com/react-fast-05-InlineIfElse.png)

codepen

https://codepen.io/ducafecat/pen/WJKLej

<p data-height="265" data-theme-id="0" data-slug-hash="WJKLej" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.3 渲染Render 三目运算" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/WJKLej/">React快速上手 3.3 渲染Render 三目运算</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 4. 循环

代码

```js
// 数据
let blogs = [
  {
    title: 'React v16.3.0: New lifecycles and context API',
    url: 'https://reactjs.org/blog/2018/03/29/react-v-16-3.html',
    by: 'Brian Vaughn'
  },
  ...
]

// 循环
function ListsView(props) {
  let datas = props.data
  return (
    <ol>
      {datas.map((item, index) => (
        <li key={index.toString()}>
          <a href={item.url} target="_blank">
            {item.title}
          </a>{' '}
          --- <small>by {item.by}</small>
        </li>
      ))}
    </ol>
  )
}

// 容器
function RenderView() {
  return (
    <div>
      <ListsView data={blogs} />
    </div>
  )
}
```

> 表达式用的 `{ ... }` 符号包裹起来
> 表达式内部的 `JSX` 用 `( ... )` 包裹
> 表达式内部的 `JSX` 也要注意必须有顶级标签
> 对于循环体内 必须有 `key` 属性，`map` 内部唯一就行

输出

![jsx-lists](http://oflimcy5e.bkt.clouddn.com/react-fast-05-lists.png)

codepen

https://codepen.io/ducafecat/pen/aGjPoQ

<p data-height="265" data-theme-id="0" data-slug-hash="aGjPoQ" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.4 渲染Render 循环" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/aGjPoQ/">React快速上手 3.4 渲染Render 循环</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 5. 嵌套循环

代码

```js
// 数据
let blogs = [
  {
    title: 'React v16.3.0: New lifecycles and context API',
    url: 'https://reactjs.org/blog/2018/03/29/react-v-16-3.html',
    by: 'Brian Vaughn'
  },
  ...
]
let types = ['推荐', '热门', '最新']

// 嵌套循环
function NestListsView(props) {
  let datas = props.data
  let types = props.types
  return (
    <ul>
      {types.map((item, index) => (
        <li key={index.toString()}>
          {item}
          <ol>
            {datas.map((item, index) => (
              <li key={index.toString()}>
                <a href={item.url} target="_blank">
                  {item.title}
                </a>{' '}
                --- <small>by {item.by}</small>
              </li>
            ))}
          </ol>
        </li>
      ))}
    </ul>
  )
}

// 容器
function RenderView() {
  return (
    <div>
      <NestListsView types={types} data={blogs} />
    </div>
  )
}
```

> 第二层在 `JSX` 中的循环需要用 `{ ... }` 包裹
> 这里的 `key` 只要同层唯一就行，不用全局唯一

输出

![jsx-nestLists](http://oflimcy5e.bkt.clouddn.com/react-fast-05-nestLists.png)

codepen

https://codepen.io/ducafecat/pen/OZqWZO

<p data-height="265" data-theme-id="0" data-slug-hash="OZqWZO" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.7 渲染Render 嵌套循环" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/OZqWZO/">React快速上手 3.7 渲染Render 嵌套循环</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

**虽然上面代码能运行，一旦代码复杂了，是不适合阅读和维护的，我们需要把 `datas` 的循环封装成组件，代码如下**

代码

```js
function ListsView(props) {
  let datas = props.data
  return (
    <ol>
      {datas.map((item, index) => (
        <li key={index.toString()}>
          {item.title}
        </li>
      ))}
    </ol>
  )
}

function NestListsView(props) {
  let datas = props.data
  let types = props.types
  return (
    <ul>
      {types.map((item, index) => (
        <li key={index.toString()}>
          {item}
          <ol>
            <ListsView data={datas}>
          </ol>
        </li>
      ))}
    </ul>
  )
}
```

> `NestListsView` 中嵌套 `ListsView` ，这样看起来舒服多了吧

### 6. 空组件

代码

```js
// 空组件
function NullView(props) {
  let isNull = props.isNull
  if (isNull) {
    return null
  }
  return <div>组件123</div>
}

// 容器
function RenderView() {
  return (
    <div>
      <NullView isNull />
    </div>
  )
}
```

> 这个空组件的作用 就是用来阻止显示，比如这里 `isNull = true` 的情况下，就没有显示的必要了

输出

![jsx-null](http://oflimcy5e.bkt.clouddn.com/react-fast-05-null.png)

codepen

https://codepen.io/ducafecat/pen/pVZqvp

<p data-height="265" data-theme-id="0" data-slug-hash="pVZqvp" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.5 渲染Render 空组件" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/pVZqvp/">React快速上手 3.5 渲染Render 空组件</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 7. ref引用

代码

```js
// ref引用
class RefInputView extends Component {
  constructor(props) {
    super(props)
    this.inputRef = React.createRef()
  }
  render() {
    return <input type="text" ref={this.inputRef} />
  }
  componentDidMount() {
    this.inputRef.current.focus()
    this.inputRef.current.value = '123'
  }
}

// 容器
function RenderView() {
  return (
    <div>
      <RefInputView />
    </div>
  )
}
```

> `ref` 提供了一种操作组件内部方法的接口, `didMount` 的时候获取焦点和设置值

输出

![jsx-ref](http://oflimcy5e.bkt.clouddn.com/react-fast-05-ref.png)

codepen

https://codepen.io/ducafecat/pen/OZwrVR

<p data-height="265" data-theme-id="0" data-slug-hash="OZwrVR" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.6 渲染Render ref引用" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/OZwrVR/">React快速上手 3.6 渲染Render ref引用</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### 8. `React.createElement` 方式

我们先看下函数定义

![React.createElement](http://oflimcy5e.bkt.clouddn.com/react-fast-05-createElement.png)

createElement函数有三个参数

参数|说明
-------|----------------
type      | html 标签名称, 默认 `input`
props     | 组件属性
children  | 组件内容、子节点

代码

```js
// 代码
function CreateElement(props) {
  let toWhat = props.toWhat
  let ele = React.createElement('div', null, `Hello ${toWhat}`)
  return ele
}

// 容器
function RenderView() {
  return (
    <div>
      <CreateElement toWhat="你好" />
    </div>
  )
}
```

输出

![createElement-output](http://oflimcy5e.bkt.clouddn.com/react-fast-05-createElement-view.png)

codepen

https://codepen.io/ducafecat/pen/QropBL

<p data-height="265" data-theme-id="0" data-slug-hash="QropBL" data-default-tab="js,result" data-user="ducafecat" data-embed-version="2" data-pen-title="React快速上手 3.8 渲染Render React.createElement" class="codepen">See the Pen <a href="https://codepen.io/ducafecat/pen/QropBL/">React快速上手 3.8 渲染Render React.createElement</a> by 会煮咖啡的猫 (<a href="https://codepen.io/ducafecat">@ducafecat</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## 代码

* [reactjs-example / 3-render.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/3-render.js)

## 参考文

* [Rendering Elements](https://reactjs.org/docs/rendering-elements.html)
* [Conditional Rendering](https://reactjs.org/docs/conditional-rendering.html)
* [JSX In Depth](https://reactjs.org/docs/jsx-in-depth.html)
* [Refs and the DOM](https://reactjs.org/docs/refs-and-the-dom.html)
* [React Without JSX](https://reactjs.org/docs/react-without-jsx.html)
