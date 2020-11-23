---
title: 静态路由
date: 2020-11-23 20:17:19
tag: nodejs
---

## 静态路由
```js
//引入http模块
const http = require('http');
//引入url模块
const url = require('url');
//引入fs文件模块
const fs = require('fs');

const server = http.createServer((req, res) => {
    let _url = url.parse(req.url);
    // http://127.0.0.1:3000/index.html?id=10
    let pathname = _url.pathname;
    
    console.log(pathname);
    if (pathname === '/favicon.ico') {
        return;
    }

    if (pathname === '/' || pathname === '/index.html') {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/html;charset=utf-8');
        fs.readFile('./html/index.html', (err,data) => {
            if (err) {
                console.log(err.message);
                return;
            }
            res.end(data);
        });
    } else if (pathname === '/list.html') {
        // console.log('客户端正在访问列表页');
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/html;charset=utf-8');
        fs.readFile('./html/list.html', (err, data) => {
            if (err) {
                console.log(err.message);
                return;
            }
            res.end(data);
        })
    } else if (pathname === '/login.html') {
        console.log('客户端正在访问登录页');
    } else {
        console.log('404');//一直报404原因，/favicon.ico
    }
});

server.listen(3000, '127.0.0.1', () => {
    console.log('服务器运行在 http://127.0.0.1:3000')
})

```
