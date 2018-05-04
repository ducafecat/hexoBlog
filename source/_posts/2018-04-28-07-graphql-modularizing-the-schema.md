---
title: GraphQL 渐进学习 07-graphql-node-server-模块化
top: false
date: 2018-04-28 14:11:48
tags: graphql
categories: GraphQL 渐进学习
thumbnail: http://oflimcy5e.bkt.clouddn.com/graphql-quick-start-thumbnail.png?imageView2/2/w/800
---

# GraphQL 渐进学习 07-graphql-node-server-模块化

## 目标

* 为了便于维护，模块化 `node 服务端` 代码
* 代码抽取层次分为
  * `schema` 定义
    * `类型`
    * `业务对象`
    * `查询`
    * `更改`
  * `resolvers` 实现
    * `查询`
    * `方法`
  * `typeResolvers` 实现
    * `自定义类型`
    * `标准类型`
  * `utils` 工具
    * `mock`

## 代码

* [graphQL-example/modular/](https://github.com/ducafecat/graphQL-example/tree/master/src/modular)

## 目录结构

```bash
.
├── index.js                                | 主程序
├── resolvers                               | 查询 更新 实现
│   ├── Mutations.js                        | 更新
│   ├── Querys.js                           | 查询
│   └── index.js                            | 入口
├── schema                                  | 模型定义
│   ├── business                            | 业务定义
│   │   ├── comment.graphql                 | 评论
│   │   └── post.graphql                    | 文章
│   └── std                                 | 类型定义
│       ├── Mutation.graphql                | 更新
│       ├── Query.graphql                   | 查询
│       ├── enum.graphql                    | 枚举
│       ├── interface.graphql               | 接口
│       ├── scalar.graphql                  | 自定义类型
│       └── union.graphql                   | 联合
├── typeResolvers                           | 类型实现
│   ├── index.js                            | 入口
│   ├── interfaces.js                       | 接口
│   ├── scalars.js                          | 自定义类型
│   ├── types.js                            | 业务类型
│   └── unions.js                           | 联合
└── utils                                   | 工具
    └── mock.js                             | 模拟
```

* 说明
  * 这是按 `Graphql` 组件层次来切分的，适合小业务项目
  * 如果是业务系统庞大，就需要外面套一层 `子业务模块` 的目录
  * 如果业务很微小，可以把目录下的多个文件合成一个，切的过于细小也带来维护的麻烦，具体尺度自己感觉哈

## 步骤

### 1. `schema` 定义采用文件扩展名 `.graphql`

规范点总有好处的，你可以写成 `.gpl` 或者 **`.graphql`**

### 2. 安装 `VSCode` 语法支持插件 `GraphQL for VSCode`

* 安装地址 [GraphQL for VSCode](https://marketplace.visualstudio.com/items?itemName=kumar-harsh.graphql-for-vscode)

* 语法高亮
  ![语法高亮](https://cdn.rawgit.com/kumarharsh/graphql-for-vscode/master/images/preview.png)

* 前去定义
  ![goto-definition](https://cdn.rawgit.com/kumarharsh/graphql-for-vscode/master/images/goto-definition.gif)

* 自动完成代码
  ![autocomplete](https://cdn.rawgit.com/kumarharsh/graphql-for-vscode/master/images/autocomplete.gif)

> 我主要用语法高亮

### 3. 安装 npm 插件 `require-graphql-file` 支持模块化 `.graphql` 文件

* 安装插件

```bash
npm i -S require-graphql-file
```

* 使用，打开 `index.js`

```js
import requireGraphQLFile from 'require-graphql-file'
...
// 读取 schema
const typeDefs = [
  requireGraphQLFile('./schema/std/scalar'),
  // requireGraphQLFile('./schema/std/enum'),
  // requireGraphQLFile('./schema/std/interface'),
  // requireGraphQLFile('./schema/std/union'),
  requireGraphQLFile('./schema/business/post'),
  requireGraphQLFile('./schema/business/comment'),
  requireGraphQLFile('./schema/std/Query'),
  requireGraphQLFile('./schema/std/Mutation')
]
...
// 合并 schema
const schema = makeExecutableSchema({
  typeDefs,
  ...
})
```

> 其实就是读取 `文本` 文件，然后合并了传参给 `makeExecutableSchema`

### 4. 编写 `schema` 定义

* 按 `业务类型` 和 `组件类型` 切分

  * 因为 `业务类型` 的文件名都是有意义的业务名称 `Post` `Comment`

  * 而 `组件类型` 的名字可以很确定 `enum` `interface` `union` `scalar` 方便查找

* 目录文件

```bash
├── schema                                  | 模型定义
│   ├── business                            | 业务定义
│   │   ├── comment.graphql                 | 评论
│   │   └── post.graphql                    | 文章
│   └── std                                 | 类型定义
│       ├── Mutation.graphql                | 更新
│       ├── Query.graphql                   | 查询
│       ├── enum.graphql                    | 枚举
│       ├── interface.graphql               | 接口
│       ├── scalar.graphql                  | 自定义类型
│       └── union.graphql                   | 联合
```

* 代码参考

  * [modular/schema/std/](https://github.com/ducafecat/graphQL-example/tree/master/src/modular/schema/std)
  * [modular/schema/business/](https://github.com/ducafecat/graphQL-example/tree/master/src/modular/schema/business)

* 主程序调用

见上文

### 5. 编写 `分解器` 代码

* 我认为 `分解器` 代码有两种
  * 1 实现 `Query查询` `Mutations更新` , 我都放在 `resolvers` 目录
  * 2 定义各种类型 `interface` `scalar` `type` `union` , 都放在 `typeResolvers` 目录

* 目录文件

```bash
├── resolvers                               | 查询 更新 实现
│   ├── Mutations.js                        | 更新
│   ├── Querys.js                           | 查询
│   └── index.js                            | 入口
├── typeResolvers                           | 类型实现
│   ├── index.js                            | 入口
│   ├── interfaces.js                       | 接口
│   ├── scalars.js                          | 自定义类型
│   ├── types.js                            | 业务类型
│   └── unions.js                           | 联合
```

* 代码参考

  * [modular/resolvers/](https://github.com/ducafecat/graphQL-example/tree/master/src/modular/resolvers)
  * [modular/typeResolvers/](https://github.com/ducafecat/graphQL-example/tree/master/src/modular/typeResolvers)

* 主程序调用

```js
import resolvers from './resolvers'
import typeResolvers from './typeResolvers'
...
// 合并 schema
const schema = makeExecutableSchema({
  typeDefs,
  resolvers,
  typeResolvers,
  resolverValidationOptions: {
    requireResolversForResolveType: false
  }
})
```

* `resolvers` `typeResolvers` 目录下写了 `index.js` 进行合并

### 6. 工具 `mock`

* 为了测试方便，我都用 `mock` 数据

如何模拟请移步 [06-graphql-采用-mockjs-mock数据](/2018/04/27/06-graphql-mock-mockjs/)

## 测试

```js
# 请求
{
  posts {
    id
    title
    content
    author
    addtime
    comments {
      id
      message
      author
      addtime
    }
  }
}

# 输出
{
  "data": {
    "posts": [
      {
        "id": 90932,
        "title": "十分长现维整",
        "content": "常气火热成或",
        "author": "文霞",
        "addtime": "10:21:14",
        "comments": [
          {
            "id": 30874,
            "message": "十称军题片格员主实",
            "author": "局反重间半何",
            "addtime": "22:05:28"
          },
          {
            "id": 7088,
            "message": "克此快候己林层省",
            "author": "备从当提属",
            "addtime": "15:45:27"
          },
          {
            "id": 74989,
            "message": "部织温制流统响教广",
            "author": "领重你包计",
            "addtime": "03:06:03"
          },
          {
            "id": 24501,
            "message": "识消己满军子酸",
            "author": "调始相内向中取造还比",
            "addtime": "05:15:38"
          },
          {
            "id": 91765,
            "message": "重开流报着色运党快但",
            "author": "儿动内心放改声口立",
            "addtime": "04:53:28"
          },
          {
            "id": 48731,
            "message": "气即感共就林始",
            "author": "进在层只作",
            "addtime": "10:29:44"
          }
        ]
      },
      {
        "id": 67359,
        "title": "开被几此图位便目作",
        "content": "应众级向由把",
        "author": "孟杰",
        "addtime": "16:49:30",
        "comments": [
          {
            "id": 28432,
            "message": "大命格我就战历",
            "author": "亲但美在号场米商",
            "addtime": "13:18:11"
          },
          {
            "id": 36258,
            "message": "红条由近的备级",
            "author": "自团人老以音状装指号",
            "addtime": "13:17:17"
          },
          {
            "id": 90117,
            "message": "么整片外习达离",
            "author": "满步该律特",
            "addtime": "16:10:35"
          },
          {
            "id": 14731,
            "message": "场等九百市很安",
            "author": "白石价和林风农",
            "addtime": "18:33:41"
          },
          {
            "id": 23523,
            "message": "二面化权文及照万",
            "author": "运现里新政过铁",
            "addtime": "17:51:59"
          },
          {
            "id": 77423,
            "message": "划群低矿流按看实养",
            "author": "个为然工些效",
            "addtime": "21:56:41"
          }
        ]
      }
    ]
  }
}
```

## 参考

* [graphql-tools/generate-schema](https://www.apollographql.com/docs/graphql-tools/generate-schema.html)

* [require-graphql-file](https://github.com/ducafecat/require-graphql-file)
