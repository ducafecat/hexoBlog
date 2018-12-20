---
title: GraphQL 渐进学习 03-GraphQL-scalar-自定义类型
top: false
date: 2018-04-26 11:54:56
tags: graphql
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 03-GraphQL-scalar-自定义类型

## 目标

* 编写自定义类型

## 代码

* [graphQL-example/features.js](https://github.com/ducafecat/graphQL-example/blob/master/src/features.js)

## 步骤

### 1. 引用 `graphql` `graphql/language`

```js
const {GraphQLScalarType} = require('graphql')
const {Kind} = require('graphql/language')
```

* `GraphQLScalarType` 用来声明  `Scalar`

* `Kind` 类型检查

### 2. 编写 `typeDefs`

```js
const typeDefs = `
  ###
  自定义日期类型
  ###
  scalar Date

  type Notice {
    content: String!
    ###
    消息时间
    ###
    noticeTime: Date!
  }
`
```

* `###...###` 是注释

* `scalar Date` 定义了自定义类型

* `Notice.noticeTime` 字段使用自定义 `Date` 类型

### 3. 编写 `resolvers`

```js
const resolvers = {
  Date: new GraphQLScalarType({
    name: 'Date',
    description: 'Date custom scalar type',
    parseValue(value) {
      return new Date(value) // value from the client
    },
    serialize(value) {
      // return new Date(value).getTime()
      return new Date(value) // value sent to the client
    },
    parseLiteral(ast) {
      if (ast.kind === Kind.INT) {
        return parseInt(ast.value, 10) // ast value is always in string format
      }
      return null
    }
  })
}
```

* `resolvers` 中需要详细声明

* `parseValue(value) {...` 客户端输入

* `serialize(value) {...` 打印给客户端

* `parseLiteral(value) {...` 检查类型

### 4. 合并 `Schema`

```js
const schema = makeExecutableSchema({
  typeDefs,
  resolvers
})
```

## 参考

* [graphql-tools](https://www.apollographql.com/docs/graphql-tools)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
