---
title: GraphQL 渐进学习 06-graphql-采用-mockjs-mock数据
top: false
date: 2018-04-27 14:53:13
tags:
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 06-graphql-采用-mockjs-mock数据

## 目标

* 开启 `graphql` `mock数据` 模式
* 采用 `mock.js` 组件进行数据模拟
* 模拟数据配置
  * 对象
  * 接口
  * 自定义类型
  * 联合
  * 查询

## 代码

* [graphQL-example/mock.js](https://github.com/ducafecat/graphQL-example/blob/master/src/mock.js)

## 步骤

### 1. 安装 `mock.js` 依赖包

```bash
npm -S install mockjs
```

### 3. `import` 所需包对象

```js
import {makeExecutableSchema, addMockFunctionsToSchema, MockList} from 'graphql-tools'
import {graphql, GraphQLScalarType} from 'graphql'
import Mock from 'mockjs'
const Random = Mock.Random
```

* `addMockFunctionsToSchema` 合成模拟函数

* `MockList` 指定模拟数据条数

* `Mock` 模拟数据生成对象

### 3. 编写 `typeDefs`

```js
const typeDefs = `
  scalar Date

  type User {
    id: Int
    name: String
    posts(limit: Int): [Post]
  }

  type Post {
    id: Int
    title: String
    views: Int
    author: User
  }

  interface Message {
    content: String
  }
  type Notice implements Message {
    content: String
    noticeTime: Date
  }
  type Remind implements Message {
    content: String
    endTime: Date
  }

  type Query {
    aString: String
    aBoolean: Boolean
    anInt: Int
    my: [User]
    author(id: Int): User
    topPosts(limit: Int): [Post]
    notices: [Notice]
  }

  type Mutation {
    addUser: User
  }
`
```

* 定义中有 `自定对象` `接口` `类型` `查询` `更新` ，复合普遍情况

### 4. 编写 `typeResolvers`

```js
const typeResolvers = {
  Message: {
    __resolveType(data) {
      return data.typename // typename property must be set by your mock functions
    }
  },
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

** `接口` `联合` `自定义类型` 需要编写 `typeResolvers` 对象 **

### 5. 合并 `Schema`

```js
const schema = makeExecutableSchema({
  typeDefs,
  typeResolvers,
  resolverValidationOptions: {
    requireResolversForResolveType: false
  }})
```

### 6. 编写模拟数据

```js
const min = 100
const max = 99999
const mocks = {
  Int: () => Random.natural(min, max),
  Float: () => Random.float(min, max),
  String: () => Random.ctitle(10, 5),
  Date: () => Random.time(),
  User: () => (
    {
      id: Random.natural(min, max),
      name: Random.cname(),
      posts: () => new MockList([6, 12]),
    }
  ),
}
```

* 可以发现都是对类型进行定义

* `MockList([6, 12])` 表示模拟 6 ~ 12 条数据

* `mock.js` 语法请移步 [Mock.js/Random](https://github.com/nuysoft/Mock/wiki/Mock.Random)

### 7. 开启模拟方式

```js
addMockFunctionsToSchema({schema, mocks, preserveResolvers: true})
```

* 没有看错就是一行代码

## 测试

### 1. 请求 `addUser` 方法

```js
# 请求
mutation {
  addUser {
    id
    name
  }
}

# 输出
{
  "data": {
    "addUser": {
      "id": 61700,
      "name": "蔡杰"
    }
  }
}
```

### 2. 请求 `me` 查询

```js
# 请求
{
  my {
    id
    name
    posts {
      id
      title
    }
  }
}

# 输出
{
  "data": {
    "my": [
      {
        "id": 10861,
        "name": "林霞",
        "posts": [
          {
            "id": 5295,
            "title": "老学通阶本照机世"
          },
          {
            "id": 74760,
            "title": "劳报办划说团可向代"
          },
          {
            "id": 96701,
            "title": "那管己单等半做状基"
          },
          {
            "id": 57025,
            "title": "值前真水员般两据和"
          },
          {
            "id": 15801,
            "title": "克文实验很即手边物"
          },
          {
            "id": 80493,
            "title": "强统消权红方"
          }
        ]
      },
      {
        "id": 22464,
        "name": "萧超",
        "posts": [
          {
            "id": 45890,
            "title": "子争安能军"
          },
          {
            "id": 6975,
            "title": "年阶高战思与称统争"
          },
          {
            "id": 52466,
            "title": "长需准之确"
          },
          {
            "id": 35372,
            "title": "手备造方专样反则"
          },
          {
            "id": 98486,
            "title": "种观点听进段周"
          },
          {
            "id": 35529,
            "title": "名据级个况交各"
          },
          {
            "id": 40534,
            "title": "要也义理平示家治"
          },
          {
            "id": 57121,
            "title": "观如王部被关约法"
          },
          {
            "id": 29170,
            "title": "求经风断治往市程"
          },
          {
            "id": 36344,
            "title": "器队热农时斯心"
          }
        ]
      }
    ]
  }
}
```

## 参考

* [graphql-tools/mocking](https://www.apollographql.com/docs/graphql-tools/mocking.html)

* [Mock.js/Random](https://github.com/nuysoft/Mock/wiki/Mock.Random)
