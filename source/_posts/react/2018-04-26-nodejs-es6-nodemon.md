---
title: nodejs 支持 es6 & 修改源码自动重启
top: false
date: 2018-04-26 09:32:17
tags: es6 nodemon
categories: node
thumbnail:
---

# nodejs 支持 es6 & 修改源码自动重启

## 1. 安装 `babel es6` 依赖包

```bash
npm i --save-dev nodemon
npm i --save-dev babel-cli
npm i --save-dev babel-plugin-transform-async-generator-functions
npm i --save-dev babel-plugin-transform-object-rest-spread
npm i --save-dev babel-plugin-transform-runtime
npm i --save-dev babel-preset-latest
```

## 2. 编写 `.babelrc`

```bash
{
  "presets": ["latest"]
}
```

## 3. 编写 `npm` `scripts`

```json
  "scripts": {
    "start": "nodemon ./src/your-es6-code.js --exec babel-node -e js"
  },
```

## 执行

```bash
npm start
```

## 完整 package.json 参考

```json
{
  "name": "nodejs es6 run",
  "version": "0.1.0",
  "description": "",
  "main": "./src/your-es6-code.js",
  "scripts": {
    "start": "nodemon ./src/your-es6-code.js --exec babel-node -e js",
    "h" : "nodemon -h"
  },
  "author": "",
  "devDependencies": {
    "babel-cli": "^6.24.0",
    "babel-plugin-transform-async-generator-functions": "^6.24.1",
    "babel-plugin-transform-object-rest-spread": "^6.23.0",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-latest": "^6.23.0",
    "nodemon": "^1.11.0"
  },
  "dependencies": {
    "body-parser": "^1.17.1",
    "express": "^4.15.2",
    "lodash": "^4.17.4"
  }
}
```

## 常见问题

### `[nodemon] app crashed - waiting for file changes before starting...`

关掉当前进程，重启即可

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Git](https://github.com/ducafecat)
