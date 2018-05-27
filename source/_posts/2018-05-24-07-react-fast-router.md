---
title: React 快速上手 - 07 前端路由 react-router
top: false
date: 2018-05-24 09:49:20
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 07 前端路由 react-router

## 目标

* 基础使用
* 参数传递
* 路由匹配
* 转换动画
* 跳转路由

## 环境

* react 16
* react-router 4
* react-router-dom 4
* react-transition-group

## 0. 安装

![react-router](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-24-10-14-20.png)

通过官网我们可以发现 `react-router` 可以用在 `web 网站端` `native 设备端`

我们这里针对 `web 网站端` 安装

```bash
yarn add react-router-dom
```

`react-router` 会包自动依赖安装

## 1. 先跑一个简单例子

* 代码示范

```js
import React, {Component} from 'react'
import {HashRouter as Router, Route, Link, Switch} from 'react-router-dom'

const Home = () => (
  <div>
    <h2>首页</h2>
  </div>
)
const About = () => (
  <div>
    <h2>关于</h2>
  </div>
)

class RouterView extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li>
              <Link to="/">首页</Link>
            </li>
            <li>
              <Link to="/about">关于</Link>
            </li>
          </ul>

          <Switch>
            <Route exact path="/" component={Home} />
            <Route path="/about" component={About} />
          </Switch>
        </div>
      </Router>
    )
  }
}

export default RouterView
```

* 代码结构

> 最外层需要包裹组件 `<Router>`
> 组件 `<Route>` 就是你看到的组件内容，放在哪里就哪里显示
> 组件 `<Link>` 是页面链接

* 动图说明

![基础路由](http://oflimcy5e.bkt.clouddn.com/react-router-1.gif)

* codepen

https://codepen.io/ducafecat/pen/GdBPqZ

## 2. 基础使用

### 2.1 `BrowserRouter` 还是 `HashRouter`

* `BrowserRouter` 是需要服务端配合, 是基于html5的pushState和replaceState的，很多浏览器不支持，存在兼容性问题。

> 链接地址长这样 `http://localhost:3000/about`

* `HashRouter` 是浏览器端解析路由

> 链接地址长这样 `http://localhost:3000#/about`

* 灵活切换

```js
import {HashRouter as Router, Route, Link, Switch} from 'react-router-dom'
//import {BrowserRouter as Router, Route, Link, Switch} from 'react-router-dom'

<Router>
  ...
</Router>
```

> 我用 `as Router` 做了别名，方便切换

### 2.2 组件 `<Route>` 属性 `exact` 完全匹配

```js
<Route path="/about" component={About} />
```

`exact=false` 的时候 `path` 等于 `/about` `/about/me` 都能匹配

但是 `exact=true` 的时候 只匹配 `path` 等于 `/about`

### 2.3 组件 `<Route>` 属性 `strict` 末尾斜杠的匹配

```js
<Route strict path="/about/" component={About} />
```

当 `strict=true` 路由请求末尾必须带 `/`

### 2.4 组件 `<Link>`

生成路由链接

* 属性 `to: string`

路由地址字符串

```js
<Link to="/about?me=haha">关于</Link>
```

* 属性 `to: object`

路由对象

```js
<Link to={{
  pathname: '/courses',
  search: '?sort=name',
  hash: '#the-hash',
  state: { fromDashboard: true }>关于</Link>
```

* 属性 `replace: bool`

设置 `true` 替换浏览器对象 `history` 为当前路由，按回退按钮时会发现之前的路由被替换了

### 2.5 组件 `<NavLink>`

生成路由链接的基础上，如果是当前路由设置激活样式

* 属性 `activeClassName: string`

样式名称

```js
<NavLink to="/about" activeClassName="selected">关于</NavLink>
```

* 属性 `activeStyle: object`

样式对象

```js
<NavLink to="/about" activeStyle={{
    fontWeight: 'bold',
    color: 'red'
   }}>关于</NavLink>
```

* 属性 `isActive: func`

判断函数

```js
const checkIsActive = (match, location) => {
  if (!match) {
    return false
  }
  ...
  return true
}

<NavLink to="/about" isActive={checkIsActive}>关于</NavLink>
```

### 2.6 组件 `<Switch>`

只渲染出第一个与当前访问地址匹配的 `<Route>` 或 `<Redirect>`

否则你有几个 `<Route>` 都会显示

### 2.7 组件 `<Redirect>`

路由重定向

```js
<Switch>
  <Redirect from='/users/:id' to='/users/profile/:id'/>
  <Route path='/users/profile/:id' component={Profile}/>
</Switch>
```

当请求 `/users/:id` 被重定向去 '/users/profile/:id'

* 属性 `from: string`

需要匹配的将要被重定向路径。

* 属性 `to: string`

重定向的 URL 字符串

* 属性 `to: object`

重定向的 location 对象

* 属性 `push: bool`

若为真，重定向操作将会把新地址加入到访问历史记录里面，并且无法回退到前面的页面。

### 2.8 组件 `<Prompt>`

当用户离开当前页面前做出一些提示。

* 属性 `message: string`
当用户离开当前页面时，设置的提示信息。

```js
<Prompt message="确定要离开？" />
```

![Prompt](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-24-13-13-17.png)

* 属性 `message: func`

当用户离开当前页面时，设置的回掉函数

```js
<Prompt message={location => `确定要去 ${location.pathname} ?`} />
```

![Prompt-func](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-24-13-17-14.png)

* 属性 `when: bool`

决定是否启用 Prompt

## 3. 参数传递

### 3.1 编写路由定义

```js
<Route path="/topics/:topicId" component={Topic} />
```

`:topicId` 定义参数

### 3.2 编写接收组件

```js
const Topic = ({match}) => (
  <div>
    <h3>参数: {match.params.topicId}</h3>
  </div>
)
```

`match.params` 就是传递的参数

### 3.3 完整例子

代码

```js
import React, {Component} from 'react'
import {BrowserRouter as Router, Route, Link, Switch} from 'react-router-dom'

const Topic = ({match}) => (
  <div>
    <h3>参数: {match.params.topicId}</h3>
  </div>
)

class RouterView extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li>
              <Link to="/topics/rendering">Rendering with React</Link>
            </li>
            <li>
              <Link to="/topics/components">Components</Link>
            </li>
            <li>
              <Link to="/topics/props-v-state">Props v. State</Link>
            </li>
          </ul>

          <Switch>
            <Route path="/topics/:topicId" component={Topic} />
          </Switch>
        </div>
      </Router>
    )
  }
}

export default RouterView
```

动图效果

![参数传递](http://oflimcy5e.bkt.clouddn.com/react-router-2.gif)

## 4. 没有匹配

代码

```js
import React, {Component} from 'react'
import {BrowserRouter as Router, Route, Link, Switch} from 'react-router-dom'

const Back = () => (
  <div>
    <h2>首页</h2>
  </div>
)

const NoMatch = () => (
  <div>
    <h2>没有匹配</h2>
  </div>
)

class RouterView extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li>
              <Link to="/back">返回</Link>
            </li>
            <li>
              <Link to="/also/will/not/match">路由请求</Link>
            </li>
          </ul>

          <Switch>
            <Route path="/back" component={Back} />
            <Route component={NoMatch} />
          </Switch>
        </div>
      </Router>
    )
  }
}

export default RouterView
```

> 在最下面写个默认组件，都没命中，就是这个了

动图效果

![没有匹配](http://oflimcy5e.bkt.clouddn.com/react-router-3.gif)

## 5. 嵌套路由

在 `react-route4` 中嵌套要这样写

```js
<Switch>
  <Route path="/article" component={ArticleList} />
  <Route path="/article/:id" component={Article} />
  <Route path="/article/:id/recommend" component={ArticleRecommend} />
</Switch>
```

写成一排，业务如下

path | 组件 | 说明
-----|------|---------
/article                  | ArticleList       | 文章列表
/article/:id              | Article           | 文章
/article/:id/recommend    | ArticleRecommend  | 文章推荐

## 6. 自定义路由

适合用来做权限检查

### 6.1 创建自定义 `Route`

```js
const isAuthenticated = true
const PrivateRoute = ({ component: Component, ...rest }) => (
  <Route
    {...rest}
    render={props =>
      isAuthenticated ? (
        <Component {...props} />
      ) : (
        <Redirect
          to={{
            pathname: "/login",
            state: { from: props.location }
          }}
        />
      )
    }
  />
);
```

### 6.2 使用自定义路由

```js
...
<Route path="/public" component={Public} />
<Route path="/login" component={Login} />
<PrivateRoute path="/protected" component={Protected} />
```

## 7. 转换动画

这里用到了 `react-transition-group` 库

动图效果

![转换动画](http://oflimcy5e.bkt.clouddn.com/react-router-4.gif)

### 7.1 安装

```js
yarn add react-transition-group
```

### 7.2 编写动画 `css`

文件 `fade.css`

```css
.fade-enter {
  opacity: 0;
  z-index: 1;
}

.fade-enter-active {
  opacity: 1;
  transition: opacity 250ms ease-in;
}

.fade-exit {
  opacity: 0;
}
```

名称 | 说明
-----|------
fade-enter        | 动画启动
fade-enter-active | 动画激活
fade-exit         | 动画离开

其它动画样式名字可以参考 [css-transition](http://reactcommunity.org/react-transition-group/css-transition)

### 7.3 导入包和动画样式

```js
...
import {TransitionGroup, CSSTransition} from 'react-transition-group'
import './fade.css'
```

`TransitionGroup, CSSTransition` 必须导入

### 7.4 编写样式

```js
const styles = {}

styles.fill = {
  position: 'relative',
  height: '200px',
  width: '500px'
}

styles.content = {
  ...styles.fill,
  top: '40px',
  textAlign: 'center',
  height: '120px'
}

styles.nav = {
  padding: 0,
  margin: 0,
  position: 'absolute',
  top: 0,
  height: '40px',
  width: '100%',
  display: 'flex'
}

styles.navItem = {
  textAlign: 'center',
  flex: 1,
  listStyleType: 'none',
  padding: '10px'
}

styles.hsl = {
  color: 'white',
  paddingTop: '20px',
  fontSize: '30px',
  height: '120px'
}

styles.rgb = {
  color: 'white',
  paddingTop: '20px',
  fontSize: '30px',
  height: '120px'
}
```

这是 `react` 的样式对象，当然你也可以写成 `.css` 文件

### 7.5 编写导航

```js
const NavLink = props => (
  <li style={styles.navItem}>
    <Link {...props} style={{color: 'inherit'}} />
  </li>
)
```

### 7.6 编写展示组件

```js
// 切换区域 A
const HSL = ({match: {params}}) => (
  <div
    style={{
      ...styles.fill,
      ...styles.hsl,
      background: `hsl(${params.h}, ${params.s}%, ${params.l}%)`
    }}
  >
    hsl({params.h}, {params.s}%, {params.l}%)
  </div>
)

// 切换区域 B
const RGB = ({match: {params}}) => (
  <div
    style={{
      ...styles.fill,
      ...styles.rgb,
      background: `rgb(${params.r}, ${params.g}, ${params.b})`
    }}
  >
    rgb({params.r}, {params.g}, {params.b})
  </div>
)
```

### 7.7 编写容器组件

```js
const RouterView = () => (
  <Router>
    <Route
      render={({location}) => (
        <div style={styles.fill}>
          <Route
            exact
            path="/"
            render={() => <Redirect to="/hsl/10/90/50" />}
          />

          <ul style={styles.nav}>
            <NavLink to="/hsl/10/90/50">Red</NavLink>
            <NavLink to="/hsl/120/100/40">Green</NavLink>
            <NavLink to="/rgb/33/150/243">Blue</NavLink>
            <NavLink to="/rgb/240/98/146">Pink</NavLink>
          </ul>

          <div style={styles.content}>
            <TransitionGroup>
              <CSSTransition key={location.key} classNames="fade" timeout={300}>
                <Switch location={location}>
                  <Route exact path="/hsl/:h/:s/:l" component={HSL} />
                  <Route exact path="/rgb/:r/:g/:b" component={RGB} />
                  <Route render={() => <div>Not Found</div>} />
                </Switch>
              </CSSTransition>
            </TransitionGroup>
          </div>
        </div>
      )}
    />
  </Router>
)
```

* codepen

https://codepen.io/ducafecat/pen/rvroYP

## 代码

* [reactjs-example / 5-1-routerBase](https://github.com/ducafecat/reactjs-example/blob/master/src/base/5-1-routerBase.js)
* [reactjs-example / 5-2-routerParams](https://github.com/ducafecat/reactjs-example/blob/master/src/base/5-2-routerParams.js)
* [reactjs-example / 5-3-routerNoMatch](https://github.com/ducafecat/reactjs-example/blob/master/src/base/5-3-routerNoMatch.js)
* [reactjs-example / 5-4-routerTransitions](https://github.com/ducafecat/reactjs-example/blob/master/src/base/5-4-routerTransitions.js)
* [reactjs-example / 5-5-routerRedirect](https://github.com/ducafecat/reactjs-example/blob/master/src/base/5-5-routerRedirect.js)

## 参考

* [react-router](https://reacttraining.com/react-router/)
* [react-transition-group](http://reactcommunity.org/react-transition-group/)
* [code-splitting-in-create-react-app](https://serverless-stack.com/chapters/code-splitting-in-create-react-app.html)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
