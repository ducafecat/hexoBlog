---
title: GraphQL 渐进学习 04-graphql-resolvers-interfaces-接口的使用
top: false
date: 2018-04-26 14:02:27
tags: graphql
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 04-graphql-resolvers-interfaces-接口的使用

## 目标

* 使用 `interfaces` 接口

## 代码

* [graphQL-example/features.js](https://github.com/ducafecat/graphQL-example/blob/master/src/features.js)

## 步骤

### 1. 准备测试静态数据

```js
const notices = [{id: 1, content: '这是 notice', noticeTime: 1524710641}]
const reminds = [{id: 1, content: '这是 remind', endTime: 1524710641}]
```

* `notices` 通知

* `reminds` 提醒

### 2. 编写 `typeDefs`

```js
const typeDefs = `
  scalar Date

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
    searchInterface (text: String!): Message!
  }
`
```

* `Message` 接口对象

* `Notice` `Remind` 对象履行接口 `Message`

* `searchInterface` 供客户端查询使用方法

### 3. 编写 `resolvers`

```js
const resolvers = {
  Query: {
    searchInterface: (_, {text}) => {
      if (text === 'notice') {
        return notices[0]
      } else {
        return reminds[0]
      }
    }
  },

  Message: {
    __resolveType(obj, context, info){
      console.log(obj, context, info)
      if(obj.noticeTime){
        return 'Notice'
      }
      if(obj.endTime){
        return 'Remind'
      }
      return null
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

* `searchInterface` 搜索方法实现，直接返回一条通知或提醒

* `__resolveType` 返回类型定义

* `if(obj.noticeTime){` 如果含有 noticeTime 字段判定是通知

### 4. 合并 `Schema`

```js
const schema = makeExecutableSchema({
  typeDefs,
  resolvers
})
```

## 测试

### 1. 请求 `searchInterface` 方法

```js
# 请求 query
query do($text: String!) {
  searchInterface(text: $text) {
    content
    ... on Notice {
      noticeTime
    }
    ... on Remind {
      endTime
    }
  }
}

# 变量 variables
{
  "text": "notice"
}

# 输出
{
  "data": {
    "searchInterface": {
      "content": "这是 notice",
      "endTime": "1970-01-18T15:31:50.641Z"
    }
  }
}
```

* 我们修改下请求 `Query`

```js
# 请求 query
query do($text: String!) {
  searchInterface(text: $text) {
    ... on Notice {
      content
      noticeTime
    }
    ... on Remind {
      content
      endTime
    }
  }
}

# 变量 variables
{
  "text": "notice"
}

# 输出
{
  "data": {
    "searchInterface": {
      "content": "这是 remind",
      "endTime": "1970-01-18T15:31:50.641Z"
    }
  }
}
```

* 可以发现返回效果是一致的，这个和 `union` 还是有区别的

> 详细请移步 [05-graphql-resolvers-union-联合的使用](/2018/04/26/05-graphql-resolvers-union/)

### 2. 打印 `__resolveType(obj, context, info)` 参数

```js
# obj
{ id: 1, content: '这是 notice', noticeTime: 1524710641 } 

# context
{}

# info
{ fieldName: 'searchInterface',
  fieldNodes:
   [ { kind: 'Field',
       alias: undefined,
       name: [Object],
       arguments: [Array],
       directives: [],
       selectionSet: [Object],
       loc: [Object] } ],
  returnType: Message,
  parentType: Query,
  path: { prev: undefined, key: 'searchInterface' },
  schema:
   GraphQLSchema {
     __allowedLegacyNames: undefined,
     _queryType: Query,
     _mutationType: Mutation,
     _subscriptionType: null,
     _directives: [ [Object], [Object], [Object] ],
     astNode: undefined,
     _typeMap:
      { Query: Query,
        Post: Post,
        Int: Int,
        String: String,
        Author: Author,
        Country: Country,
        Message: Message,
        MessageResult: MessageResult,
        Notice: Notice,
        Date: Date,
        Remind: Remind,
        Mutation: Mutation,
        AuthorInput: AuthorInput,
        __Schema: __Schema,
        __Type: __Type,
        __TypeKind: __TypeKind,
        Boolean: Boolean,
        __Field: __Field,
        __InputValue: __InputValue,
        __EnumValue: __EnumValue,
        __Directive: __Directive,
        __DirectiveLocation: __DirectiveLocation },
     _implementations: { Message: [Array] },
     __validationErrors: [],
     _possibleTypeMap: { Message: [Object] } },
  fragments: {},
  rootValue: undefined,
  operation:
   { kind: 'OperationDefinition',
     operation: 'query',
     name: { kind: 'Name', value: 'do', loc: [Object] },
     variableDefinitions: [ [Object] ],
     directives: [],
     selectionSet: { kind: 'SelectionSet', selections: [Array], loc: [Object] },
     loc: { start: 0, end: 160 } },
  variableValues: { text: 'notice' } }
```

## 参考

* [graphql-tools](https://www.apollographql.com/docs/graphql-tools)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
