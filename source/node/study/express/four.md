---
title: express发送文件
date: 2020-11-23 20:42:19
tag: nodejs
---

```js
const app = require('express')();

// app.get('/', (req,res) => {
//     //发送文件
//     //文件路径必须是绝对路径
//     console.log(__dirname);
//     res.sendFile(`${__dirname}/html/index.html`);
// })

//发送json数据
// app.get('/', (req, res) => {
//     res.json({
//         name: '张三',
//         age: 20
//     });
// });
// app.get('/', (req, res) => {
//     res.sendFile(`${__dirname}/package.json`);
// });

//重定向
app.get('/', (req, res) => {
    // res.redirect('/index');//默认302跳转
    res.redirect(301,'/index');
});
app.get('/index',(req,res) => {
    res.sendFile(`${__dirname}/html/index.html`)
})

//结束响应
res.end()

app.listen(3000);
```