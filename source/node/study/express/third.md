---
title: 路由处理程序
date: 2020-11-23 20:38:19
tag: nodejs
---

```js
//引入express模块
const express = require('express');

//创建应用程序
const app = express();

//路由处理程序

//多处理程序
let handler1 = (req,res,next) => {
    console.log('handler1');
    //向下继续执行
    next();
};

let handler2 = (req,res) => {
    res.send('<h1>成功响应</h1>')
};

app.get('/', [handler1, handler2])

//监听服务端口
app.listen(3000);
```