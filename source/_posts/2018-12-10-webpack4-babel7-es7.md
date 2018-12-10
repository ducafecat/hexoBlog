---
title: webpack4 配置 Babel7
top: false
date: 2018-12-10 14:39:00
tags:
categories:
thumbnail:
---

# webpack4 配置 Babel7

## 1. 下载包

```sh
yarn add @babel/core @babel/preset-env babel-loader
```

## 2. 编写 webpack.config.js

```js
var config = {
  entry: './index.js', // Webpack
  output: {
    path: __dirname,
    filename: 'block.build.js',
  },
  module : {
    rules : [
      {
        test: /.js$/,
        loader: 'babel-loader',
        exclude: /node_modules/
      }
    ]
  }
};
module.exports = config;
```

## 3. 编写 .babelrc

```json
{
  "presets": ["@babel/preset-env"],
  "plugins": [["@babel/plugin-proposal-class-properties", {"loose": false}]]
}
```

## 其它可选配置

```json
{
  "plugins": [
    // Stage 1
    "@babel/plugin-proposal-export-default-from",
    "@babel/plugin-proposal-logical-assignment-operators",
    ["@babel/plugin-proposal-optional-chaining", { "loose": false }],
    ["@babel/plugin-proposal-pipeline-operator", { "proposal": "minimal" }],
    ["@babel/plugin-proposal-nullish-coalescing-operator", { "loose": false }],
    "@babel/plugin-proposal-do-expressions",

    // Stage 2
    ["@babel/plugin-proposal-decorators", { "legacy": true }],
    "@babel/plugin-proposal-function-sent",
    "@babel/plugin-proposal-export-namespace-from",
    "@babel/plugin-proposal-numeric-separator",
    "@babel/plugin-proposal-throw-expressions",

    // Stage 3
    "@babel/plugin-syntax-dynamic-import",
    "@babel/plugin-syntax-import-meta",
    ["@babel/plugin-proposal-class-properties", { "loose": false }],
    "@babel/plugin-proposal-json-strings"
  ]
}
```

## 错误提示原文

```js
ERROR in ./src/index.js
Module build failed (from ./node_modules/babel-loader/lib/index.js):
Error: [BABEL] /Users/hans/Desktop/vue03/src/index.js:
As of v7.0.0-beta.55, we've removed Babel's Stage presets.
Please consider reading our blog post on this decision at
https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets
for more details. TL;DR is that it's more beneficial in the
  long run to explicitly add which proposals to use.

For a more automatic migration, we have updated babel-upgrade,
https://github.com/babel/babel-upgrade to do this for you with
"npx babel-upgrade".

If you want the same configuration as before:

{
  "plugins": [
    // Stage 0
    "@babel/plugin-proposal-function-bind",

    // Stage 1
    "@babel/plugin-proposal-export-default-from",
    "@babel/plugin-proposal-logical-assignment-operators",
    ["@babel/plugin-proposal-optional-chaining", { "loose": false }],
    ["@babel/plugin-proposal-pipeline-operator", { "proposal": "minimal" }],
    ["@babel/plugin-proposal-nullish-coalescing-operator", { "loose": false }],
    "@babel/plugin-proposal-do-expressions",

    // Stage 2
    ["@babel/plugin-proposal-decorators", { "legacy": true }],
    "@babel/plugin-proposal-function-sent",
    "@babel/plugin-proposal-export-namespace-from",
    "@babel/plugin-proposal-numeric-separator",
    "@babel/plugin-proposal-throw-expressions",

    // Stage 3
    "@babel/plugin-syntax-dynamic-import",
    "@babel/plugin-syntax-import-meta",
    ["@babel/plugin-proposal-class-properties", { "loose": false }],
    "@babel/plugin-proposal-json-strings"
  ]
}

If you're using the same configuration across many separate projects,
keep in mind that you can also create your own custom presets with
whichever plugins and presets you're looking to use.

module.exports = function() {
  return {
    plugins: [
      require("@babel/plugin-syntax-dynamic-import"),
      [require("@babel/plugin-proposal-decorators"), { "legacy": true }],
      [require("@babel/plugin-proposal-class-properties"), { "loose": false }],
    ],
    presets: [
      // ...
    ],
  };
};
   (While processing: "/Users/hans/Desktop/vue03/node_modules/@babel/preset-stage-0/lib/index.js")
    at _default (/Users/hans/Desktop/vue03/node_modules/@babel/preset-stage-0/lib/index.js:9:9)
    at loadDescriptor (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/config/full.js:165:14)
    at cachedFunction (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/config/caching.js:33:19)
    at loadPresetDescriptor (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/config/full.js:235:63)
    at config.presets.reduce (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/config/full.js:77:21)
    at Array.reduce (<anonymous>)
    at recurseDescriptors (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/config/full.js:74:38)
    at loadFullConfig (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/config/full.js:108:6)
    at process.nextTick (/Users/hans/Desktop/vue03/node_modules/@babel/core/lib/transform.js:28:33)
    at process._tickCallback (internal/process/next_tick.js:61:11)
Child html-webpack-plugin for "index.html":
     1 asset
    Entrypoint undefined = index.html
    [./node_modules/html-webpack-plugin/lib/loader.js!./src/index.html] 1.18 KiB {0} [built]
    [./node_modules/webpack/buildin/global.js] (webpack)/buildin/global.js 472 bytes {0} [built]
    [./node_modules/webpack/buildin/module.js] (webpack)/buildin/module.js 497 bytes {0} [built]
        + 1 hidden module
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! vue03@1.0.0 build: `webpack`
npm ERR! Exit status 2
npm ERR!
npm ERR! Failed at the vue03@1.0.0 build script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/hans/.npm/_logs/2018-12-10T06_34_07_108Z-debug.log
```

## 参考

- [Removing Babel's Stage Presets](https://babeljs.io/blog/2018/07/27/removing-babels-stage-presets)

----

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
