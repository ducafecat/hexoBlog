---
title: React 快速上手 - 08 redux 状态管理 react-redux
top: false
date: 2018-05-25 10:55:21
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 08 redux 状态管理 react-redux

今天我会快速的过一下 `redux` 知识，然后讨论下 `适用性`，最后为了让大家能快速学习，简化了官方的经典 Demo `todo` ，跟着我做一遍就行。

## 目标

* 了结什么是 redux
* 什么情况下适用
* 创建一个 redux 应用
* 安装调试插件

## 闲话

`redux` 是一个很深的话题，先聊聊标准学习路线，打开 [https://redux.js.org/introduction](https://redux.js.org/introduction)

笔者为了写这文章，可是安静的读了一遍

首先 `Introduction` 7 篇

![redux-introduction](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-11-06-46.png)

好像懂了什么，不知道呢

然后 `Base` 6 篇

![redux-Base](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-11-09-32.png)

其实 [Example: Todo List](https://redux.js.org/basics/example-todo-list) 这才是最有帮助的

又听了视频 [Getting Started with Redux 视频](https://egghead.io/courses/getting-started-with-redux)

好啰嗦啊

![Redux 视频 Getting Started with Redux](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-13-55-28.png)

**不过这也是学习的正确姿势，总要看一遍官方文档，无奈官网设计的更像一个学术站点，而不是类库工具一类的指导说明**

好了好了~吐槽完毕，开始正文！

## redux 是什么, 可以不用么

![ducafecat_2018-05-25-11-27-37](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-11-27-37.png)

这张图是 [Flux](https://facebook.github.io/flux/docs) 官网的，画的很好。

`Redux` 是一种 `数据的管理` 方式，界面上发起各种操作 `Action` ，然后 `Dispatcher` 到 `Store` 更新状态 `State`，推送新状态到视图 `View`

好了，概念一句话说完了，来看看什么情况下用 `Redux`

![斗鱼界面](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-11-39-21.png)

这个界面，如果用 `React` 来实现，在底部的 `容器组件` 要处理的业务有: 用户登录、弹幕、主播信息、视频进度、道具、打赏、IM聊天、等等还有很多 而且随着产品迭代，功能只会多

按我们之前的组件拆分，结构是合理，但是这个数据管理麻烦了，各种业务数据压入子组件，各种业务事件返回到主容器组件，可能的代码结构如下

```js
// 状态
this.state = {
  data1:...,
  data2:...,
  data3:...,
  data...n:...
}

// 事件
function handelEvent1 = {...}
function handelEvent2 = {...}
function handelEvent3 = {...}
function handelEvent...n = {...}

// JSX
<主视图组件>
  <用户信息 data1={this.state.data1} handleEven1={...} handleEven2={...} handleEven...n={...} />
  <主播信息>
    <基础资料 data...n={...} handleEven...n={...} />
    <头像 data...n={...} handleEven...n={... />
    <关注 data...n={...} handleEven...n={... />
    <标签 data...n={...} handleEven...n={... />
    <热度 data...n={...} handleEven...n={... />
    ...
  </主播信息>
  <播放器 ...>
    <... />
    ...
  <播放器>
    ...
  <...播放器/>
  ...
</主视图组件>
```

**会发现组件套组件，父父子子的，完全没法维护了，梳理这些关系就很费时间，而且容易错误**

`Redux` 就是来解决这个问题的，每个组件只要执行自己的 `Action` ，不用返回到父容器

* `Redux` 在弹幕业务中就两步：
  1. `弹幕发出组件` 执行发出弹幕动作 `Action` 内容 `{ type: 'BARRAGE_SEND', text: '弹幕消息' }`
  2. `弹幕滚动组件` 新弹幕数据被更新到弹幕滚动组件

但是也不好滥用，我看到有些简单的 表单操作，竟然也套了 `Redux` ，完全没必要，自己把握吧

## 动手创建一个 redux 应用

来个经典例子 `todo` , 原版 [Todo](https://redux.js.org/basics/example-todo-list) , 我这里是精简版，那么我们开始

* 动图效果

![react-redux-todo](http://oflimcy5e.bkt.clouddn.com/react-redux-todo.gif)

* 组件结构

![todo 组件结构](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-13-40-15.png)

### 第一步: 编写 Action

界面上产生的操作

```js
let nextTodoId = 0

export const addTodo = text => ({
  type: 'ADD_TODO',
  id: nextTodoId++,
  text
})

export const toggleTodo = id => ({
  type: 'TOGGLE_TODO',
  id
})
```

* 事件的格式
  * `type` 字段必须有，表示做什么操作
  * `type` 值全局唯一
  * `type` 大写定义
  * 其它字段自由定义

### 第二步: 编写 Reducers

事件对应的响应处理，处理完后返回新 `state`

```js
const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ]
    case 'TOGGLE_TODO':
      return state.map(
        todo =>
          todo.id === action.id ? {...todo, completed: !todo.completed} : todo
      )
    default:
      return state
  }
}

export default todos
```

### 第三步: 合并 Reducers

使用 `combineReducers` 合并所有的处理过程

```js
import { combineReducers } from 'redux'
import todos from './todos'

export default combineReducers({
  todos
})
```

假设你有其它业务, 如: 用户 `user` , 购物车 `cart`

```js
import { combineReducers } from 'redux'
import todos from './todos'
import user from './user'
import cart from './cart'

export default combineReducers({
  todos,
  user,
  cart
})
```

### 第四步: 编写组件 `AddTodo`

* 使用 `connect` 连接组件
* 使用 `dispatch` 方法派发事件

```js
import React from 'react'
import { connect } from 'react-redux'
import { addTodo } from '../redux/actions'

const AddTodo = ({ dispatch }) => {
  let input

  return (
    <div>
      <form onSubmit={e => {
        e.preventDefault()
        if (!input.value.trim()) {
          return
        }
        dispatch(addTodo(input.value))
        input.value = ''
      }}>
        <input ref={node => input = node} />
        <button type="submit">
          Add Todo
        </button>
      </form>
    </div>
  )
}

export default connect()(AddTodo)
```

`dispatch(addTodo(input.value))` 就发动了 `Redux` 到 `Reducers` , 这时 `state` 更新了

### 第五步: 编写组件 `TodoList`

再来个稍微复杂的

```js
import React from 'react'
import {connect} from 'react-redux'
import {toggleTodo} from '../redux/actions'

const Todo = ({onClick, completed, text}) => (
  <li
    onClick={onClick}
    style={{
      textDecoration: completed ? 'line-through' : 'none'
    }}
  >
    {text}
  </li>
)

const TodoList = ({todos, toggleTodo}) => (
  <ul>
    {todos.map(todo => (
      <Todo key={todo.id} {...todo} onClick={() => toggleTodo(todo.id)} />
    ))}
  </ul>
)

const mapStateToProps = state => ({
  todos: state.todos
})

const mapDispatchToProps = dispatch => ({
  toggleTodo: id => dispatch(toggleTodo(id))
})

export default connect(mapStateToProps, mapDispatchToProps)(TodoList)

```

还是 `connect` 方法 , 这里重点讲下

先看看官方定义 [connect()](https://github.com/reduxjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)

* 定义

```js
connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])
```

* 参数

名称|说明
-----|-------
mapStateToProps       | `store` 绑定 `state` , 做更新的需要传入
mapDispatchToProps    | 绑定派发事件 `event` , 不传的话默认 `dispatch` 对象, 就像上面的 `AddTodo` 组件
mergeProps            | `[mergeProps(stateProps, dispatchProps, ownProps): props] (Function):` 合并属性自定义，自己不传的话默认 `·Object.assign` ，我也是放空默认的
options               | 一些选项，我没怎么在意，知道有就行

`[mapStateToProps], [mapDispatchToProps]` 这两个用到的多，大家自己练习下

### 第六步: 容器组件

```js
import React from 'react'
import AddTodo from './AddTodo'
import TodoList from './TodoList'

const App = () => (
  <div>
    <AddTodo />
    <TodoList />
  </div>
)

export default App
```

这里简单，并列排放

### 最后: 适配 `App`

```js
import React, {Component} from 'react'
import {createStore} from 'redux'
import {Provider} from 'react-redux'
import TodoApp from './todos/components/App'
import todoReducer from './todos/redux/reducers'

const store = createStore(todoReducer)

class BaseRedux extends Component {
  render() {
    return (
      <Provider store={store}>
        <TodoApp />
      </Provider>
    )
  }
}

export default BaseRedux
```

* 标准代码格式
  1. `createStore(reducers)` 创建 `store`
  2. `Provider` 适配器压入 `store` 对象, 子节点都受 `Redux` 控制

## 调试工具 `Redux DevTools extension`

动图效果

![Redux DevTools extension](http://oflimcy5e.bkt.clouddn.com/react-redux-dev-tools.gif)

### 1. 安装 chrome 插件

打开 [redux-devtools](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)

![redux-devtools](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-15-13-59.png)

### 2. 配置代码

```js
const store = createStore(
  todoReducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
)
```

`createStore` 时加入 `window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()`

### 3. 开启插件

打开 `chrome` 调试工具，点击面板 `Redux`

![chrome-tab-Redux](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-25-15-20-29.png)

## codepen

https://codepen.io/ducafecat/pen/RyBEeK

## 代码

* [reactjs-example / 6-redux.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/6-redux.js)

## 参考

* [redux 官网](https://redux.js.org/)
* [flux 官网](https://facebook.github.io/flux/)
* [Getting Started with Redux 视频](https://egghead.io/courses/getting-started-with-redux)
* [Building React Applications with Idiomatic Redux 视频](https://egghead.io/courses/building-react-applications-with-idiomatic-redux)
* [Redux 中文文档](http://www.redux.org.cn/)
* [Example: Todo List](https://redux.js.org/basics/example-todo-list)
* [Redux DevTools extension](http://extension.remotedev.io/)
* [zalmoxisus/redux-devtools-extension](https://github.com/zalmoxisus/redux-devtools-extension)
