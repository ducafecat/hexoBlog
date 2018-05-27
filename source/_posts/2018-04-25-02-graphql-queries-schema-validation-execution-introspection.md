---
title: GraphQL 渐进学习 02-GraphQL-组件构成知识整理
top: false
date: 2018-04-25 10:25:14
tags: graphql
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 02-GraphQL-组件构成知识整理

## 目标

* 对 GraphQL 构成的组件进行知识精要整理
* 组件构成有 5 个方面
  * 查询变更 queries
  * Schema 和类型 schema
  * 验证 validation
  * 执行 execution
  * 内省 introspection

> 方便日后当手册查阅

## 代码

融合上述特性写了个代码，方便记忆

* [graphQL-example/features.js](https://github.com/ducafecat/graphQL-example/blob/master/src/features.js)

## 查询变更 queries

> **查询变更都是客户端代码**

### 字段（Fields）

```js
{
  authors {
    id
    firstName
    lastName
  }
}
```

### 参数（Arguments）

```js
{
  author(id: 1) {
    id
    firstName
    lastName
  }
}
```

### 别名（Aliases）

```js
{
  author(id: 1) {
    id
    name: firstName
    lastName
  }
}
```

### 片段（Fragments）

```js
{
  author(id: 1) {
    ...authorFields
  }
}

fragment authorFields on Author {
  id
  firstName
  lastName
  posts {
    title
  }
}
```

### 操作名称（Operation name）

```js
query findAuthors {
  authors {
    id
    firstName
    lastName
    posts {
      title
    }
  }
}
```

### 变量（Variables）

> 输入

```js
# query
query findAuthor($id: Int!) {
  author(id: $id) {
    id
    firstName
    lastName
    posts {
      title
    }
  }
}

# variables
{
  "id": 1
}
```

> 输出

![Operation name](http://oflimcy5e.bkt.clouddn.com/graphql-02-queries-operation.png)

### 默认变量（Default variables）

```js
query findAuthor($id: Int = 2) {
  author(id: $id) {
    id
    firstName
    lastName
    posts {
      title
    }
  }
}
```

### 指令（Directives）

```js
# query
query findAuthor($id: Int = 1, $withPosts: Boolean!) {
  author(id: $id) {
    id
    firstName
    lastName
    posts @include(if: $withPosts) {
      title
    }
  }
}

# variables
{
  "withPosts": false
}
```

* `@include(if: Boolean)` 仅在参数为 `true` 时，包含此字段。

* `@skip(if: Boolean)` 如果参数为 `true` 时，跳过此字段。

### 变更（Mutations）

```js
# query
mutation upPost($id: Int!) {
  upvotePost(postId: $id) {
    id
    title
    votes
  }
}

# variables
{
  "id": 1
}
```

### 变更中的多个字段（Multiple fields in mutations）

```js
# query
mutation upPost($id: Int!) {
  upVotePost(postId: $id) {
    id
    title
    votes
  }
  clearVotePost(postId: $id) {
    id
    title
    votes
  }
}

# variables
{
  "id": 1
}

# output
{
  "data": {
    "upVotePost": {
      "id": 1,
      "title": "Introduction to GraphQL",
      "votes": 1
    },
    "clearVotePost": {
      "id": 1,
      "title": "Introduction to GraphQL",
      "votes": 0
    }
  }
}
```

* 多个变更，按顺序先后执行，但是查询是并行的

### 内联片段（Inline Fragments）

```js
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
}
```

### 元字段（Meta fields）

```js
{
  authors {
    __typename
    id
    firstName
    lastName
  }
}
```

* `__typename` 显示类型名称

* 其它元字段，用于描述 `内省` 系统

## Schema 和类型 schema

> **schema 定义是服务端代码**

### 对象类型和字段（Object Types and Fields）

```js
  type Author {
    """
    流水编号
    """
    id: Int!
    firstName: String
    lastName: String
    """
    用户发布的文章
    """
    posts: [Post]
  }
```

* `type` 定义对象

* `"""` 标记注释

* `Int` `String` 系统对象

* `!` 不能为空

* `[`...`]` 数组

* `String!` 字符串不为空

* `[String]!` 数组不为空

### 默认标量类型

* `Int` 有符号 32 位整数

* `Float` 有符号双精度浮点值

* `String` UTF‐8 字符序列

* `Boolean` true 或者 false

* `ID` ID 标量类型表示一个唯一标识符，通常用以重新获取对象或者作为缓存中的键。ID 类型使用和 String 一样的方式序列化；然而将其定义为 ID 意味着并不需要可读型。

### 自定义类型 `scalar`

```js
scalar Date
```

> 详细请移步 [03-GraphQL-scalar-自定义类型](/2018/04/26/03-graphql-custom-scalar-type/)

### 枚举类型（Enumeration Types）

```js
  enum Country {
    CN
    ENG
    JP
    UK
    CA
  }

  type Author {
    """
    流水编号
    """
    id: Int!
    firstName: String
    lastName: String
    state(state: Country = CN): String
    """
    the list of Posts by this author
    """
    posts: [Post]
  }
```

* `state` 字段指定了枚举类型，默认值 `CN`

### 接口（Interfaces）

```js
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
```

> 详细请移步 [04-graphql-resolvers-interfaces-接口的使用](/2018/04/26/04-graphql-resolvers-interfaces/)

### 联合类型（Union Types）

```js
union MessageResult = Notice | Remind
```

> 详细请移步 [05-graphql-resolvers-union-联合的使用](/2018/04/26/05-graphql-resolvers-union/)

### 输入类型（Input Types）

```js
# 定义输入类型 `AuthorInput`
input AuthorInput {
  firstName: String
  lastName: String
  state: String
}

# 指定变更变量类型
type Mutation {
  addAuthor (
    author: AuthorInput!
  ): Author
}
```

## 验证 validation

通过使用类型系统，你可以预判一个查询是否有效。这让服务器和客户端可以在无效查询创建时就有效地通知开发者，而不用依赖运行时检查。

## 执行 execution

一个 GraphQL 查询在被验证后，GraphQL 服务器会将之执行，并返回与请求的结构相对应的结果，该结果通常会是 JSON 的格式。

```js
Query: {
  human(obj, args, context) {
    return context.db.loadHumanByID(args.id).then(
      userData => new Human(userData)
    )
  }
}
```

* `obj` 上一级对象，如果字段属于根节点查询类型通常不会被使用。

* `args` 可以提供在 GraphQL 查询中传入的参数。

* `context` 会被提供给所有解析器，并且持有重要的上下文信息比如当前登入的用户或者数据库访问对象。

## 内省 introspection

### `__schema` 查询哪些对象可用

```js
# 查询
{
  __schema {
    types {
      name
      kind
    }
  }
}

# 输出
{
  "data": {
    "__schema": {
      "types": [
        {
          "name": "Query",
          "kind": "OBJECT"
        },
        {
          "name": "Post",
          "kind": "OBJECT"
        },
        {
          "name": "Int",
          "kind": "SCALAR"
        },
        {
          "name": "String",
          "kind": "SCALAR"
        },
        {
          "name": "Author",
          "kind": "OBJECT"
        },
        {
          "name": "Country",
          "kind": "ENUM"
        },
        {
          "name": "Message",
          "kind": "INTERFACE"
        },
        {
          "name": "MessageResult",
          "kind": "UNION"
        },
        {
          "name": "Notice",
          "kind": "OBJECT"
        },
        {
          "name": "Date",
          "kind": "SCALAR"
        },
        {
          "name": "Remind",
          "kind": "OBJECT"
        },
        {
          "name": "Mutation",
          "kind": "OBJECT"
        },
        {
          "name": "AuthorInput",
          "kind": "INPUT_OBJECT"
        },
        {
          "name": "__Schema",
          "kind": "OBJECT"
        },
        {
          "name": "__Type",
          "kind": "OBJECT"
        },
        {
          "name": "__TypeKind",
          "kind": "ENUM"
        },
        {
          "name": "Boolean",
          "kind": "SCALAR"
        },
        {
          "name": "__Field",
          "kind": "OBJECT"
        },
        {
          "name": "__InputValue",
          "kind": "OBJECT"
        },
        {
          "name": "__EnumValue",
          "kind": "OBJECT"
        },
        {
          "name": "__Directive",
          "kind": "OBJECT"
        },
        {
          "name": "__DirectiveLocation",
          "kind": "ENUM"
        }
      ]
    }
  }
}
```

* __Schema, __Type, __TypeKind, __Field, __InputValue, __EnumValue, __Directive - 这些有着两个下划线的类型是内省系统的一部分

### `queryType` 查询对象名称

```js
# 查询
{
  __schema {
    queryType {
      name
    }
  }
}

# 输出
{
  "data": {
    "__schema": {
      "queryType": {
        "name": "Query"
      }
    }
  }
}
```

### `__type` 查询对象

```js
# 查询
{
  __type(name: "Notice") {
    name
    kind
    description
    fields {
      name
      type {
        name
      }
      description
    }
    interfaces {
      name
      description
    }
  }
}

# 输出
{
  "data": {
    "__type": {
      "name": "Notice",
      "kind": "OBJECT",
      "description": "通知对象",
      "fields": [
        {
          "name": "content",
          "type": {
            "name": "String"
          },
          "description": "通知内容"
        },
        {
          "name": "noticeTime",
          "type": {
            "name": "Date"
          },
          "description": "通知时间"
        }
      ],
      "interfaces": [
        {
          "name": "Message",
          "description": "消息接口"
        }
      ]
    }
  }
}
```

### 所有的查询对象

```js
# 查询
{
  __type(name: "Query") {
    name
    kind
    fields {
      name
      description
      args {
        name
        description
        defaultValue
      }
    }
  }
}

# 输出
{
  "data": {
    "__type": {
      "name": "Query",
      "kind": "OBJECT",
      "fields": [
        {
          "name": "posts",
          "description": "所有文章",
          "args": []
        },
        {
          "name": "authors",
          "description": "所有作者",
          "args": []
        },
        {
          "name": "author",
          "description": "",
          "args": [
            {
              "name": "id",
              "description": "作者ID",
              "defaultValue": null
            }
          ]
        },
        {
          "name": "searchInterface",
          "description": "",
          "args": [
            {
              "name": "text",
              "description": "",
              "defaultValue": null
            }
          ]
        },
        {
          "name": "searchUnion",
          "description": "",
          "args": [
            {
              "name": "text",
              "description": "",
              "defaultValue": null
            }
          ]
        }
      ]
    }
  }
}
```

## 参考

* [Queries and Mutations](http://graphql.org/learn/queries/)
* [Schemas and Types](http://graphql.org/learn/schema/)
* [Validation](http://graphql.org/learn/validation/)
* [Execution](http://graphql.org/learn/execution/)
* [Introspection](http://graphql.org/learn/introspection/)
* [apollodata simple](https://launchpad.graphql.com/1jzxrj179)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
