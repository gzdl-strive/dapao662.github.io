---
title: 引入静态资源--加载css js 图片--动态路由方式
date: 2020-11-23 20:23:19
tag: nodejs
---

```js
//引入http模块
const http = require('http');
//引入url模块
const url = require('url');
//引入fs文件模块
const fs = require('fs');
//引入path模块
const path = require('path');

const server = http.createServer((req, res) => {
    let _url = url.parse(req.url);
    let pathname = _url.pathname;

    let extname = path.extname(pathname);
    //mime类型设置
    let mime = 'text/html;charset=utf-8';
    let base = './assets';

    if (extname === '.jpg') {
        mime = 'image/jpg';
    } else if (extname === '.js') {
        mime = 'text/javascript';
    } else if (extname === '.css') {
        mime = 'text/css';
    }

    console.log(pathname);
    if (pathname === '/favicon.ico') {
        return;
    }
    //文件读取
    res.writeHead(200, {'Content-Type':mime});
    fs.readFile(base+pathname, (err, data) => {
        if (err) {
            console.log(err.message);
        }
        res.end(data);
    })
});

server.listen(3000, '127.0.0.1', () => {
    console.log('服务器运行在 http://127.0.0.1:3000')
})

```
