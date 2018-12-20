---
title: React 快速上手 - 10 类型检查 PropTypes
top: false
date: 2018-05-28 14:28:38
tags: react
categories: React 快速上手
thumbnail: http://oflimcy5e.bkt.clouddn.com/reactjs-logo-2.jpeg
---

# React 快速上手 - 10 类型检查 PropTypes

## 目标

* 了解类型检查的重要性
* 类型检查的使用及调试
* 类型检查的一些技巧

## 环境

* react 16.3.2
* prop-types 15.6.1

## 我们为什么要 类型检查

我们先来看一份 [rollbar](https://rollbar.com) 公司对 `1000+` 项目的错误回收分析 `top10`

文章参考: [Top 10 JavaScript errors from 1000+ projects ](https://rollbar.com/blog/top-10-javascript-errors/)

![rollbar_err_top10](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-28-14-48-35.png)

几乎都是因为类型而发生的错误

比如定义 `let prodList = []` , 你代码中 `prodList.push(...)`

但是初始的时候被设置 `prodList = 123` 而且没有任何提示, 那运行 `push(...)` 肯定要报未知方法的错误

这种问题在自由的 `JavaScript` 世界很普遍，如果在 `编译` `运行` 两个阶段都没提示的话，排错是很麻烦的，全靠经验和对业务的熟悉 (陈年老项目又没文档简直是地狱)

所以我们要把问题消灭在萌芽中，就是申明对象的时候同时把类型也定义掉了， `react` 的自带方案是 `PropTypes` 组件

当然还有 `Flow` `TypeScript` 我个人比较喜欢 `TypeScript` 这在以后的进阶文章我再写

`PropTypes` 是一个在 `编码` 阶段提供类型检查的方案 有提示总是好的，那我们开始

## `PropTypes` 使用

老样子，先来个基础简单的例子

### 1 简单例子

* 导入包

```js
import PropTypes from 'prop-types'
```

* 编写组件

```js
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>
  }
}
```

* 编写类型检查

```js
Greeting.propTypes = {
  name: PropTypes.string
}
```

> 设置 `name` 属性类型为 `string` 字符串

* 容器

```js
<div>
  <Greeting name={123} />
</div>
```

显然这里设置数字和定义不一致

* 页面打印

![Greeting-string](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-28-15-34-04.png)

页面能正常显示，没有错误，这是因为错误以 `console` 方式反馈

* 页面错误

![Greeting-string-err](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-28-15-36-06.png)

好吧~ 有提示就好哈~

### 2 不同的验证器

```js
// 数组、布尔、函数、数字、对象、字符串、symbol
MyComponent.propTypes = {
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  // 任何东西都可以被渲染:numbers, strings, elements,或者是包含这些类型的数组(或者是片段)。
  optionalNode: PropTypes.node,

  // 一个 React 元素。
  optionalElement: PropTypes.element,

  // 你也可以声明一个 prop 是类的一个实例。
  // 使用 JS 的 instanceof 运算符。
  optionalMessage: PropTypes.instanceOf(Message),

  // 你可以声明 prop 是特定的值，类似于枚举
  optionalEnum: PropTypes.oneOf(['News', 'Photos']),

  // 一个对象可以是多种类型其中之一
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]),

  // 一个某种类型的数组
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // 属性值为某种类型的对象
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),

  // 一个特定形式的对象
  optionalObjectWithShape: PropTypes.shape({
    color: PropTypes.string,
    fontSize: PropTypes.number
  }),

  // 你可以使用 `isRequired' 链接上述任何一个，以确保在没有提供 prop 的情况下显示警告。
  requiredFunc: PropTypes.func.isRequired,

  // 任何数据类型的值
  requiredAny: PropTypes.any.isRequired,

  // 你也可以声明自定义的验证器。如果验证失败返回 Error 对象。不要使用 `console.warn` 或者 throw ，
  // 因为这不会在 `oneOfType` 类型的验证器中起作用。
  customProp: function(props, propName, componentName) {
    if (!/matchme/.test(props[propName])) {
      return new Error(
        'Invalid prop `' + propName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  },

  // 也可以声明`arrayOf`和`objectOf`类型的验证器，如果验证失败需要返回Error对象。
  // 会在数组或者对象的每一个元素上调用验证器。验证器的前两个参数分别是数组或者对象本身，
  // 以及当前元素的键值。
  customArrayProp: PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
    if (!/matchme/.test(propValue[key])) {
      return new Error(
        'Invalid prop `' + propFullName + '` supplied to' +
        ' `' + componentName + '`. Validation failed.'
      );
    }
  })
};
```

### 3 限制单个子代

代码

```js
// 组件
const OnlyOneChild = props => {
  const children = props.children
  return <div>{children}</div>
}
OnlyOneChild.propTypes = {
  children: PropTypes.element.isRequired
}

// 容器
<OnlyOneChild>
  <h3>第一个子节点</h3>
  <h3>第二个子节点</h3>
</OnlyOneChild>
```

打印

![OnlyOneChild](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-28-15-49-36.png)

### 4 属性默认值

代码

```js
// 组件
const DefaultVal = props => {
  return <h3>{props.name}</h3>
}
DefaultVal.defaultProps = {
  name: '我是默认值!'
}

// 容器
<div>
  <DefaultVal />
</div>
```

如果父组件没有设置并传入 `name` ，`defaultProps` 将确保 `this.props.name` 将有一个默认值。

打印

![defaultProps](http://oflimcy5e.bkt.clouddn.com/ducafecat_2018-05-28-15-52-00.png)

## 代码

* [8-propTypes.js](https://github.com/ducafecat/reactjs-example/blob/master/src/base/8-propTypes.js)

## 参考

* [Top 10 JavaScript errors from 1000+ projects](https://rollbar.com/blog/top-10-javascript-errors/)
* [prop-types](https://www.npmjs.com/package/prop-types)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
