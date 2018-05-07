---
title: GraphQL 渐进学习 08-graphql-采用eggjs-服务端开发
top: false
date: 2018-05-02 17:13:41
tags: graphql eggjs
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 08-graphql-采用eggjs-服务端开发

## 软件环境

* eggjs 2.2.1

> 请注意当前的环境，老版本的 `egg` 可能配置有差异

## 目标

* 创建 graphql 服务
* 用户登录授权
* 用户访问鉴权

## 代码

* [ducafecat/eggjs-graphql-example/egg-server](https://github.com/ducafecat/eggjs-graphql-vue-example/tree/master/egg-server)

## 步骤

### 1 使用 `egg-graphql`

* 安装包

```bash
npm i --save egg-graphql
```

* 开启插件 `/config/plugin.js`

```js
exports.graphql = {
  enable: true,
  package: 'egg-graphql'
}
```

* 配置插件 `/config/config.default.js`

```js
// add your config here
config.middleware = ['graphql']

// graphql
config.graphql = {
  router: '/graphql',
  // 是否加载到 app 上，默认开启
  app: true,
  // 是否加载到 agent 上，默认关闭
  agent: false,
  // 是否加载开发者工具 graphiql, 默认开启。路由同 router 字段。使用浏览器打开该可见。
  graphiql: true,
  // graphQL 路由前的拦截器
  onPreGraphQL: function* (ctx) {},
  // 开发工具 graphiQL 路由前的拦截器，建议用于做权限操作(如只提供开发者使用)
  onPreGraphiQL: function* (ctx) {},
}
```

### 2 `egg-graphql` 代码结构

```bash
.
├── graphql                       | graphql 代码
│   ├── common                    | 通用类型定义
│   │   ├── resolver.js           | 合并所有全局类型定义
│   │   ├── scalars               | 自定义类型定义
│   │   │   └── date.js           | 日期类型实现
│   │   └── schema.graphql        | schema 定义
│   ├── mutation                  | 所有的更新
│   │   └── schema.graphql        | schema 定义
│   ├── query                     | 所有的查询
│   │   └── schema.graphql        | schema 定义
│   └── user                      | 用户业务
│       ├── connector.js          | 连接数据服务
│       ├── resolver.js           | 类型实现
│       └── schema.graphql        | schema 定义
```

* graphql 目录下，有 **4** 种代码
  * 1 `common` 全局类型定义
  * 2 `query` 查询代码
  * 3 `mutation` 更新操作代码
  * 4 `业务` 实现代码
    * 4.1 `connector` 连接数据服务
    * 4.2 `resolver` 类型实现
    * 4.3 `schema` 定义

### 3 编写 `common` 全局类型

* 1 `common/schema.graphql`

```js
scalar Date
```

* 2 `common/scalars/date.js`

```js
const { GraphQLScalarType } = require('graphql');
const { Kind } = require('graphql/language');

module.exports = new GraphQLScalarType({
  name: 'Date',
  description: 'Date custom scalar type',
  parseValue(value) {
    return new Date(value);
  },
  serialize(value) {
    return value.getTime();
  },
  parseLiteral(ast) {
    if (ast.kind === Kind.INT) {
      return parseInt(ast.value, 10);
    }
    return null;
  },
});
```

* 3 `common/resolver.js`

```js
module.exports = {
  Date: require('./scalars/date'), // eslint-disable-line
};
```

> 在 `egg node` 下还是用 `require` ，如果语言偏好用 `import` 会损失转换性能，不推荐

### 4 编写 `user` 业务

* `user/schema.graphql`

```js
# 用户
type User {
  # 流水号
  id: ID!
  # 用户名
  name: String!
  # token
  token: String
}
```

* `user/connector.js`

```js
'use strict'

const DataLoader = require('dataloader')

class UserConnector {
  constructor(ctx) {
    this.ctx = ctx
    this.loader = new DataLoader(this.fetch.bind(this))
  }

  fetch(id) {
    const user = this.ctx.service.user
    return new Promise(function(resolve, reject) {
      const users = user.findById(id)
      resolve(users)
    })
  }

  fetchById(id) {
    return this.loader.load(id)
  }

  // 用户登录
  fetchByNamePassword(username, password) {
    let user = this.ctx.service.user.findByUsernamePassword(username, password)
    return user
  }

  // 用户列表
  fetchAll() {
    let user = this.ctx.service.user.findAll()
    return user
  }

  // 用户删除
  removeOne(id) {
    let user = this.ctx.service.user.removeUser(id)
    return user
  }

}

module.exports = UserConnector
```

> `dataloader` 是 `facebook` 出品的数据请求缓存 解决 `N+1` 问题

* `user/resolver.js`

```js
'use strict'

module.exports = {
  Query: {
    user(root, {username, password}, ctx) {
      return ctx.connector.user.fetchByNamePassword(username, password)
    },
    users(root, {}, ctx) {
      return ctx.connector.user.fetchAll()
    }
  },
  Mutation: {
    removeUser(root, { id }, ctx) {
      return ctx.connector.user.removeOne(id)
    },
  }
}
```

### 5 编写 `query` 查询

* `query/schema.graphql`

```js
type Query {
  # 用户登录
  user(
    # 用户名
    username: String!,
    # 密码
    password: String!
    ): User
  # 用户列表
  users: [User!]
}
```

### 6 编写 `mutation` 更新

* `mutation/schema.graphql`

```js
type Mutation {

  # User
  # 删除用户
  removeUser (
    # 用户ID
    id: ID!): User
}

```

### 7 开启 `cros` 跨域访问

* `config/plugin.js`

```js
exports.cors = {
  enable: true,
  package: 'egg-cors'
}
```

* `config/config.default.js`

```js
// cors
config.cors = {
  origin: '*',
  allowMethods: 'GET,HEAD,PUT,POST,DELETE,PATCH'
}
// csrf
config.security = {
  csrf: {
    ignore: () => true
  }
}
```

> 作为 `API` 服务，顺手把 `csrf` 关掉

### 8 编写数据服务 `jwt` 授权

* 配置 `config/config.default.js`

```js
// easy-mock 模拟数据地址
config.baseURL =
  'https://www.easy-mock.com/mock/59801fd8a1d30433d84f198c/example'

// jwt
config.jwt = {
  jwtSecret: 'shared-secret',
  jwtExpire: '14 days',
  WhiteList: ['UserLogin']
}
```

* 数据请求封装 `util/request.js`

```js
'use strict'

const _options = {
  dataType: 'json',
  timeout: 30000
}

module.exports = {

  createAPI: (_this, url, method, data) => {
    let options = {
      ..._options,
      method,
      data
    }
    return _this.ctx.curl(
      `${_this.config.baseURL}${url}`,
      options
    )
  }
}
```

* 用户数据服务 `service/user.js`

```js
const Service = require('egg').Service
const {createAPI} = require('../util/request')
const jwt = require('jsonwebtoken')

class UserService extends Service {

  // 用户详情
  async findById(id) {
    const result = await createAPI(this, '/user', 'get', {
      id
    })
    return result.data
  }

  // 用户列表
  async findAll() {
    const result = await createAPI(this, '/user/all', 'get', {})
    return result.data
  }

  // 用户登录、jwt token
  async findByUsernamePassword(username, password) {
    const result = await createAPI(this, '/user/login', 'post', {
      username,
      password
    })
    let user = result.data
    user.token = jwt.sign({uid: user.id}, this.config.jwt.jwtSecret, {
      expiresIn: this.config.jwt.jwtExpire
    })
    return user
  }

  // 用户删除
  async removeUser(id) {
    const result = await createAPI(this, '/user', 'delete', {
      id
    })
    return result.data
  }
}

module.exports = UserService
```

### 9 `token` 验证中间件

* 配置  `config/config.default.js`

```js
config.middleware = ['auth', 'graphql']

config.bodyParser = {
  enable: true,
  jsonLimit: '10mb'
}
```

> 开启内置 `bodyParser` 服务

* 编写 `middleware/auth.js`

```js
const jwt = require('jsonwebtoken')

module.exports = options => {
  return async function auth(ctx, next) {
    // 开启 GraphiQL IDE 调试时，所有的请求放过
    if (ctx.app.config.graphql.graphiql) {
      await next()
      return
    }
    const body = ctx.request.body
    if (body.operationName !== 'UserLogin') {
      let token = ctx.request.header['authorization']
      if (token === undefined) {
        ctx.body = {message: '令牌为空，请登陆获取！'}
        ctx.status = 401
        return
      }
      token = token.replace(/^Bearer\s/, '')
      try {
        let decoded = jwt.verify(token, ctx.app.config.jwt.jwtSecret, {
          expiresIn: ctx.app.config.jwt.jwtExpire
        })
        await next()
      } catch (err) {
        ctx.body = {message: '访问令牌鉴权无效，请重新登陆获取！'}
        ctx.status = 401
      }
    } else {
      await next()
    }
  }
}
```

> 如果开启 `GraphiQL IDE` 工具，`token` 验证将失效，令牌数据是写在 `request.header[authorization]`，这个调试 `IDE` 不支持设置 `header`

## 参考

### 1 文章

* [Koa2 手册](https://github.com/koajs/koa)
* [eggjs 手册](https://eggjs.org/zh-cn/)
* [graphql-js/Authentication and Express Middleware](https://graphql.org/graphql-js/authentication-and-express-middleware/)
* [Egg/GraphQL](https://zhuanlan.zhihu.com/p/30604868)
* [facebook/GraphQL](http://facebook.github.io/graphql/October2016/)
* [apollo-graphql](https://www.apollographql.com/)
* [GraphQL API v4](https://developer.github.com/v4/)
* [github/authenticating-with-graphql](https://developer.github.com/v4/guides/forming-calls/#authenticating-with-graphql)

### 2 组件

* [eggjs/egg-graphql](https://github.com/eggjs/egg-graphql)
* [eggjs/egg-cors](https://github.com/eggjs/egg-cors)
* [facebook/dataloader](https://github.com/facebook/dataloader)
* [koajs/cors](https://github.com/koajs/cors)
* [vue-apollo](https://github.com/Akryum/vue-apollo)
* [apollo-client](https://www.npmjs.com/package/apollo-client)
* [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)
* [ApolloLink Set headers](https://github.com/Akryum/vue-apollo/issues/144)
* [element-ui](https://github.com/ElemeFE/element)
