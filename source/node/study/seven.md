---
title: ejs
date: 2020-11-23 20:248:19
tag: nodejs
---

>npm install ejs --save
```js
const http = require('http');
//引入ejs模块
const ejs = require('ejs');

const server = http.createServer((req,res) => {
    res.writeHead(200, {'Content-Type': 'text/html;charset:utf-8'});

    //渲染模板
    if (req.url === '/index') {
        ejs.renderFile('./views/index.ejs', {msg: 'hello ejs', list: ['item1', 'item2', 'item3', 'item4'], isLogin: false}, (err,str) => {
            if (err) {
                console.log(err.message);
                return;
            }
            res.end(str);
        })
    } else {
        ejs.renderFile('./views/article.ejs', {}, (err,str) => {
            if (err) {
                console.log(err.message);
                return;
            }
            res.end(str);
        })
    }


})
server.listen(3000, '127.0.0.1', () => {
    console.log('服务器运行于 http:127.0.0.1:3000');
})
```
**index.ejs**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>首页</title>
</head>
<body>
    <h1>输出标签</h1>
    <h1><%= msg %>
    <ul>
        <% list.forEach(item =>{ %>
        <li><%= item %></li>
        <% }) %>
    </ul>
    <h1>脚本标签</h1>
    <p>
        <% if(isLogin) { %>
        <span>你好，请登录</span>
        <% } else { %>
        <span>你好，退出</span>
        <% } %>
    </p>
    <h1>导入模板</h1>
    <%- include('header') -%> 
    <div>index.ejs</div>
    <%- include('footer') -%> 
</body>
</html>
```