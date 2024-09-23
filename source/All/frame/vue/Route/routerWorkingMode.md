---
title: 路由器的两种工作模式
date: 2021-10-17 13:30:02
toc: true
---

## Hash模式
- 对于一个url来说，什么是hash值？--- #及其后面的内容就是hash值。

- hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。

- hash模式：
  1. 地址中永远带着#号，不美观 。
  2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
  3. 兼容性较好。

## History模式
1. 地址干净，美观
2. 兼容性和hash模式相比略差
3. 应用部署时需要后端人员支持，解决刷新页面服务端404的问题

```js
//node.js写的服务器
const express = require('express');
const history = require('connect-history-api-fallback');//用于解决路由刷新被当成资源取请求问题
const app = express();
app.use(history())
app.use(express.static(__dirname + '/static'))

app.get('/demo', (req, res) => {
  res.send({
    name: 's1mple',
    age: 26,
    weapon: 'AWP,AK,M4,A1...'
  });
  res.end();
})

app.listen(5005, (err) => {
  if (!err) {
    console.log('服务器已经启动，地址为 http://localhost:5005');
  }
})
```