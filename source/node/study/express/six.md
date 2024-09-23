---
title: express获取请求参数
date: 2020-11-23 20:46:19
tag: nodejs
---

```js
const express = require('express');
const app = express();

//中间件
app.use(express.static('static'));

//获取请求参数
//get
//http://127.0.0.1:3000/user?id=10
app.get('/user', (req, res) => {
    console.log(req.query.id);
    res.end();
})

//restful分格
//http://127.0.0.1:3000/user/10
app.get('/user/:id', (req, res) => {
    console.log(req.params.id);
})

//post
//中间件
app.use(express.urlencoded({extended: true}));
app.post('/login',(req,res) => {
    console.log(req.body.username,req.body.password);
    res.end();
})

app.listen(3000);
```