---
title: express路由路径匹配
date: 2020-11-23 20:38:19
tag: nodejs
---

```js
//引入express模块
const express = require('express');

//创建应用程序
const app = express();

//路由路径匹配
//固定字符串匹配
// app.get('/index', (req,res) => {
//     res.send('<h1>成功响应</h1>')
// });
//通配模式
// * => 0~多个任意的字符
// app.get('/user/*', (req, res) => {
//     res.send('<h1>通配模式</h1>')
// });
// ? => 前一个字符或组可以出现0~1次
// app.get('/ab?c', (req,res) => {
//     res.send('<h1>?字符</h1>')
// })
// + => 前一个字符或组可以出现1~多次
// app.get('/ab+c', (req,res) => {
//     res.send('<h1>+字符</h1>')
// })
// () => 组
// app.get('/a(bc)?c', (req,res) => {
//     res.send('<h1>组</h1>')
// })

//正则匹配模式
app.get(/.*\.html$/, (req,res) => {
    res.send('<h1>正则</h1>')
})

//监听服务端口
app.listen(3000);
```