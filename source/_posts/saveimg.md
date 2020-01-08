---
title: 使用axios读取远程图片保存到本地
date: 2017-01-19 16:15:47
tags: [图片, axios]
categories: node.js
---

使用 axios 读取远程图片保存到本地

```js
const axios = require('axios')
var fs = require('fs')

axios
  .get('http://*****.win/img/random/2.png', {
    responseType: 'arraybuffer'
  })
  .then(function(response) {
    console.log(response.status)
    console.log(response.headers)
    fs.writeFile('out.png', response.data, function(err) {
      if (err) throw err
      console.log('保存成功')
    })
  })
```
