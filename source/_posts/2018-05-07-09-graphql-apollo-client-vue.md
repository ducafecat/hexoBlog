---
title: GraphQL 渐进学习 09-graphql-apollo-client-vue
top: false
date: 2018-05-07 11:15:21
tags: graphql vue
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 09-graphql-apollo-client-vue-客户端开发

## 软件环境

* vue > 2.5.x

## 目标

* 创建 graphql 客户端
* 封装请求处理
* 基于 token 验证

## 代码

* [ducafecat/eggjs-graphql-example/vue-client](https://github.com/ducafecat/eggjs-graphql-vue-example/tree/master/vue-client)

## 步骤

### 1 安装依赖包

* `npm install`

```bash
npm install --save vue-apollo graphql apollo-client apollo-link apollo-link-http apollo-cache-inmemory graphql-tag
```

* `webpack.json` 包清单

```json
  "dependencies": {
    "apollo-cache-inmemory": "^1.2.0",
    "apollo-client": "^2.3.0",
    "apollo-link": "^1.2.2",
    "apollo-link-http": "^1.5.4",
    "element-ui": "^2.3.7",
    "graphql": "^0.13.2",
    "graphql-tag": "^2.9.2",
    "vue": "^2.5.2",
    "vue-apollo": "^3.0.0-beta.5",
    "vue-router": "^3.0.1"
  },
```

> 如果遇到代码编译问题，请对照我使用的包版本

### 2 编写 `webpack` 配置

* 文件 `build/webpack.base.conf.js`

```json
  module: {
    rules: [
      ...
      {
        test: /\.(graphql|gql)$/,
        exclude: /node_modules/,
        loader: 'graphql-tag/loader'
      }
      ...
    ]
  }
```

* 当 `import` 时，对 `graphql` `gql` 扩展名自动 `schema js` 包装

* 如 `import QUERY_USER from '@/graphql/user.graphql'` ，把 `QUERY_USER` 打印看看

### 3 编写 `config.json` 配置

* 文件 `src/utils/config.json`

```json
{
  "graphqlServer": "http://127.0.0.1:7001/graphql",
  "tokenName": "UU_AUTH_TOKEN"
}
```

* `graphqlServer` 服务器地址

* `tokenName` 本地写 `localStorage` `key` 名称 ，推荐大家做本地化可以用 `cookie` 设置过期时间

### 4 编写 `VueApollo` 适配器

* 文件 `src/apolloProvider.js`

```js
import Vue from 'vue'
import {ApolloClient} from 'apollo-client'
import {HttpLink} from 'apollo-link-http'
import {InMemoryCache} from 'apollo-cache-inmemory'
import VueApollo from 'vue-apollo'
import { ApolloLink, concat, split } from 'apollo-link';
import { getMainDefinition } from 'apollo-utilities';
import Config from '@/utils/config.json'

Vue.use(VueApollo)

const httpLink = new HttpLink({
  uri: Config.graphqlServer,
})

const authMiddleware = new ApolloLink((operation, forward) => {
  const token = localStorage.getItem(Config.tokenName) || null
  operation.setContext({
    headers: {
      Authorization: `Bearer ${token}`
    }
  });
  return forward(operation);
})

const apolloClient = new ApolloClient({
  link: concat(authMiddleware, httpLink),
  cache: new InMemoryCache(),
  connectToDevTools: true,
})

const apolloProvider = new VueApollo({
  defaultClient: apolloClient,
  defaultOptions: {
    $loadingKey: 'loading'
  },
  errorHandler (error) {
    console.log('Global apollo error handler')
    console.error(error)
  }
})

export default apolloProvider
```

> `authMiddleware` 中写入 `Request headers token`
> `apolloClient` 中定义 `cache: new InMemoryCache()`

* 文件 `src/main.js`

```js
import apolloProvider from './apolloProvider'
...
new Vue({
  el: '#app',
  provide: apolloProvider.provide(),
  ...
})
```

### 5 编写 `schema` 定义

* 项目中的请求定义 我都放在了 目录 `src/graphql` 下，有点像 `restful` 的 `api` 定义

```bash
.
├── removeUser.graphql
├── user.graphql
└── users.graphql
```

* 我这里写了 2 个查询 1 个更新操作，名称和服务端定义一致，这样方便查询修改

### 6 编写 `Graphql` 请求

* 文件 `src/components/HelloWorld.vue`

* 官方 `readme.md` 中只写了一种 `组件` 方式调用，我还是喜欢 `api` 方式

* 代码 [HelloWorld.vue](https://github.com/ducafecat/eggjs-graphql-vue-example/blob/master/vue-client/src/components/HelloWorld.vue)

#### 6.1 `组件` 方式

```js
  apollo: {
    login: {
      query: gql`
        query queryFun($username: String!, $password: String!) {
          user(username: $username, password: $password) {
            id
            name
            token
          }
        }
      `,
      variables() {
        return {
          username: 'ducafecat',
          password: '345457yftgyhdsfghre'
        }
      },
      manual: true,
      result({data, loading}) {
        console.log(data, loading)
        if (!loading) {
          this.res = data
        }
      }
    }
  }
}
</script>
```

这种方式做查询，适合功能单一的展示组件，灵活性感觉还是差了点，有兴趣的可以去掉注释大家体会下。

#### 6.2 `api` 方式

* `query` 查询

```js
import QUERY_USER from '@/graphql/user.graphql'
...
  methods: {
    handleLogin() {
      this.clearData()
      this.$apollo
        .query({
          // Query
          query: QUERY_USER,
          variables: {
            username: 'ducafecat',
            password: '12321321321321432'
          },
        })
        .then(response => {
          this.loading = false
          this.res = response.data
          localStorage.setItem(Config.tokenName, this.res.user.token)
          alert('登录成功，写入Token完成，重新装载 apolloProvider')
          window.location.reload()
        })
        .catch(error => {
          this.loading = false
          this.err = error
        })
    },
```

* `mutate` 更新

```js
    handleRemoveUser() {
      this.clearData()
      this.$apollo
        .mutate({
          // Query
          mutation: MUTATION_REMOVE_USER,
          variables: {
            id: 123
          }
        })
        .then(response => {
          this.loading = false
          this.res = response.data
        })
        .catch(error => {
          this.loading = false
          this.err = error
        })
    },
```

## 参考

### 1 文章

* [vue-apollo](https://github.com/Akryum/vue-apollo)
* [Egg/GraphQL](https://zhuanlan.zhihu.com/p/30604868)
* [facebook/GraphQL](http://facebook.github.io/graphql/October2016/)
* [GraphQL API v4](https://developer.github.com/v4/)
* [github/authenticating-with-graphql](https://developer.github.com/v4/guides/forming-calls/#authenticating-with-graphql)
* [VueConf 2017 demo](https://github.com/Akryum/vueconf-2017-demo)

### 2 组件

* [vue-apollo](https://github.com/Akryum/vue-apollo)
* [apollo-client](https://www.npmjs.com/package/apollo-client)
* [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)
* [ApolloLink Set headers](https://github.com/Akryum/vue-apollo/issues/144)
* [element-ui](https://github.com/ElemeFE/element)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
