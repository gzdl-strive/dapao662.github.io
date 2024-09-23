---
title: express基本使用
date: 2020-11-23 20:35:19
tag: nodejs
---

```js
//引入express模块
const express = require('express');

//创建应用程序
const app = express();

//路由
//GET
app.get('/', (req, res) => {
    //发送到客户端
    res.send('GET / 1');//两个一样的路由匹配第一个
});
app.get('/', (req, res) => {
    //发送到客户端
    res.send('GET / 2');
});

//POST
app.post('/', (req,res) => {
    res.send('POST /');
})

//all
app.all('/', (req,res) => {
    res.send('all /')
});

//监听服务端口
app.listen(3000);
```