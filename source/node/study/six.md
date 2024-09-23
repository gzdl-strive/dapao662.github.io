---
title: Node
date: 2020-11-23 20:25:19
tag: nodejs
---

>下载npm install mime --save
```js
//引入http模块
const http = require('http');
//引入url模块
const url = require('url');
//引入fs文件模块
const fs = require('fs');
//引入path模块
const path = require('path');
//引入依赖项目mime
const mime = require('mime');

const server = http.createServer((req, res) => {
    let _url = url.parse(req.url);
    let pathname = _url.pathname;

    //mime类型设置
    let mimeStr = mime.getType(pathname);
    console.log('pathname:'+pathname);
    console.log('mimeStr:'+mimeStr);
    let base = './assets';

    if (pathname === '/favicon.ico') {
        return;
    }
    //文件读取
    res.writeHead(200, {'Content-Type':mimeStr});
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