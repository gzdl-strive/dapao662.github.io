---
title: 静态资源
date: 2020-11-23 20:20:19
tag: nodejs
---

## 静态资源

```js
//引入http模块
const http = require('http');
//引入url模块
const url = require('url');
//引入fs文件模块
const fs = require('fs');

const server = http.createServer((req, res) => {
    let _url = url.parse(req.url);
    let pathname = _url.pathname;
    if (pathname === '/favicon.ico') {
        return;
    }

    if (pathname === '/' || pathname === '/index.html') {
        res.statusCode = 200;
        res.setHeader('Content-Type', 'text/html;charset=utf-8');
        // fs.readFile('./html/index.html', (err,data) => { //静态路由
        fs.readFile('./html'+pathname, (err,data) => { //动态路由
            if (err) {
                console.log(err.message);
                return;
            }
            res.end(data);
        });
    } else if (pathname === '/style.css') {
        res.writeHead(200, {'Content-Type':'text/css'});//和上方两句话等效
        fs.readFile('./html'+pathname, (err, data) => {
            if (err) {
                console.log(err.message);
            }
            res.end(data);
        })
    } else if (pathname === '/public.js') {
        res.writeHead(200, {'Content-Type':'text/javascript'});//和上方两句话等效
        fs.readFile('./html'+pathname, (err, data) => {
            if (err) {
                console.log(err.message);
            }
            res.end(data);
        })
    } else if (pathname === '/yellow.jpg') {
        res.writeHead(200, {'Content-Type':'image/jpg'});//和上方两句话等效
        fs.readFile('./html'+pathname, (err, data) => {
            if (err) {
                console.log(err.message);
            }
            res.end(data);
        })
    }
});

server.listen(3000, '127.0.0.1', () => {
    console.log('服务器运行在 http://127.0.0.1:3000')
})

```


