---
title: GraphQL 渐进学习 01-GraphQL-快速上手
top: false
date: 2018-04-24 11:36:48
tags: graphql
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 01-GraphQL-快速上手

## 目标

* 创建一个 `Node Express GraphQL server`
* 采用可视化 `GraphiQL IDE` 进行测试

## 代码

* [ducafecat/graphQL-example](https://github.com/ducafecat/graphQL-example)

## 创建一个 node 项目

```bash
mkdir graphql-example
cd graphql-example
npm init
```

> 开发环境可以参考 [nodejs 支持 es6 & 修改源码自动重启](/2018/04/26/nodejs-es6-nodemon/)

## 安装依赖包

```bash
npm install --save apollo-server-express graphql-tools graphql express body-parser
```

## 运行官方示例

> 为了突出关键点，省略完整代码
>
> 完整代码移步 [quick-start.js](https://github.com/ducafecat/graphQL-example/blob/master/quick-start.js)

### 1 创建文件 `quick-start.js`

```bash
vi quick-start.js
```

### 2 定义数据结构

```js
// GraphQL schema
const typeDefs = `
  type Query { books: [Book] }
  type Book { title: String, author: String }
`
```

### 3 定义处理函数

```js
// resolvers
const resolvers = {
  Query: {books: () => books}
}
```

### 4 合并定义

```js
const schema = makeExecutableSchema({
  typeDefs,
  resolvers
})
```

### 5 路由 `GraphQL 数据服务`

```js
app.use('/graphql', bodyParser.json(), graphqlExpress({schema}))
```

### 6 路由 `GraphIQL IDE`

```js
app.use('/graphiql', graphiqlExpress({endpointURL: '/graphql'}))
```

## 运行

### 启动服务

```bash
node quick-start.js
```

![npm script](http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-script.png)

### 运行 `GraphiQL IDE` 编辑器 

> 输入网址 [http://localhost:3000/graphiql](http://localhost:3000/graphiql)

![GraphiQL IDE](http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-graphiql.png)

> 你可以试着改变查询条件

```js
{
  books {
    title
    author
  }
}
```

## 与服务器通讯

### 抓包看看

![GraphQL Request](http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-post.png)

* `URL` 是代码中定义的路由 `Request URL: http://localhost:3000/graphql?`

* `GraphiQL IDE` 默认用 `POST` 方式

* 请求数据体

```json
{
  "query": "{\n  books {\n    title\n    author\n  }\n}\n",
  "variables": null,
  "operationName": null
}
```

> `query` 查询体
> `variables` 参数
> `operationName` 操作名称

## 参考

* [Quick start](https://www.apollographql.com/docs/apollo-server/example.html)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
