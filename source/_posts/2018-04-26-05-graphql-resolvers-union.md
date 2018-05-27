---
title: GraphQL 渐进学习 05-graphql-resolvers-union-联合的使用
top: false
date: 2018-04-26 14:21:53
tags: graphql
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 05-graphql-resolvers-union-联合的使用

## 目标

* 使用 `union` 联合

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

  """
  联合类型 通知 & 提醒
  """
  union MessageResult = Notice | Remind

  type Query {
    searchUnion (text: String!): MessageResult!
  }
`
```

* `MessageResult` 联合对象

* `searchUnion` 供客户端查询使用方法

### 3. 编写 `resolvers`

```js
const resolvers = {
  Query: {
    searchUnion: (_, {text}) => {
      if (text === 'notice') {
        return notices[0]
      } else {
        return reminds[0]
      }
    }
  },

  MessageResult: {
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

* `searchUnion` 搜索方法实现，直接返回一条通知或提醒

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

### 1. 请求 `searchUnion` 方法

```js
# 请求 query
query do($text: String!) {
  searchUnion(text: $text) {
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
      "content": "这是 notice",
      "endTime": "1970-01-18T15:31:50.641Z"
    }
  }
}
```

* 我们可以试着把 `content` 字段提出，发现查询报错，这个和 `interface` 还是有区别的

```js
# 错误
{
  "errors": [
    {
      "message": "Cannot query field \"content\" on type \"MessageResult\". Did you mean to use an inline fragment on \"Message\", \"Notice\", or \"Remind\"?",
      "locations": [
        {
          "line": 3,
          "column": 5
        }
      ]
    }
  ]
}
```

> 详细请移步 [04-graphql-resolvers-interfaces-接口的使用](/2018/04/26/04-graphql-resolvers-interfaces/)

### 2. 打印 `__resolveType(obj, context, info)` 参数

```js
# obj
{ id: 1, content: '这是 notice', noticeTime: 1524710641 } 

# context
{}

# info
{ fieldName: 'searchUnion',
  fieldNodes:
   [ { kind: 'Field',
       alias: undefined,
       name: [Object],
       arguments: [Array],
       directives: [],
       selectionSet: [Object],
       loc: [Object] } ],
  returnType: MessageResult!,
  parentType: Query,
  path: { prev: undefined, key: 'searchUnion' },
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
     _possibleTypeMap: { MessageResult: [Object] } },
  fragments: {},
  rootValue: undefined,
  operation:
   { kind: 'OperationDefinition',
     operation: 'query',
     name: { kind: 'Name', value: 'do', loc: [Object] },
     variableDefinitions: [ [Object] ],
     directives: [],
     selectionSet: { kind: 'SelectionSet', selections: [Array], loc: [Object] },
     loc: { start: 0, end: 173 } },
  variableValues: { text: 'notice' } }
```

## 参考

* [graphql-tools](https://www.apollographql.com/docs/graphql-tools)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
