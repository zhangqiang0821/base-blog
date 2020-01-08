---
title: babel6
date: 2017-01-21 14:20:46
tags: [babel]
categories: node.js
---

2 在 node.js 中使用 babel6 的方法.

1. 安装 babel6

   > npm install babel-core --save

2. 安装插件
   > npm install babel-preset-es2015

<!-- more -->

3. 配置 bedel6

   方法 1 . 新建 .babelrc 文件

   ```js
   {
   "presets": ["es2015"]
   }
   ```

   方法 2 在 pageage.json 里面配置

   ```js
   "babel": {
     "presets": ["es2015"]
   }
   ```

4. 新建 index.js

```js
require('babel-core/register')
require('./app.js')
```

5. 运行
   > node index.js

### koa2+async 直接使用 runkoa

> npm i -g runkoa
> runkoa app.js

### koa 生成器,一键生成 koa 和 koa2 项目

[koa-generator](https://github.com/17koa/koa-generator)
