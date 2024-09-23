---
title: 长连接
date: 2021-04-11 14:56:20
tog: http
toc: true
---


### TCP connection
* Connection: keep-alive/close(开启/关闭)
* HTTP2只需要建立一个TCP长连接(同域下)

### 示例
```html
<!-- index.html -->
    <img src="./1.png" alt="">
    <img src="./2.png" alt="">
    <img src="./3.png" alt="">
    <img src="./4.png" alt="">
    <img src="./5.png" alt="">
    <img src="./6.png" alt="">
    <img src="./7.png" alt="">
    <img src="./8.png" alt="">
    <img src="./9.png" alt="">
    <img src="./11.png" alt="">
    <img src="./12.png" alt="">
```
```js
//server.js
const http = require('http');
const fs = require('fs');

http.createServer(function (req, res) {
    if (req.url === '/') {
        const html = fs.readFileSync('index.html');
        res.writeHead(200, {
            'Content-Type': 'text/html'
        })
        res.end(html);
    } else {
        const img = fs.readFileSync('1.png');
        res.writeHead(200, {
            'Content-Type': 'image/png'
        })
        res.end(img);
    }
}).listen(8888);
console.log('server start at http://127.0.0.1:8888');
```
![keep-alive1](/assets/httpImg/keepAlive1.png "keep-alive1")
>Chrome浏览器最多允许对同一个域名Host建立6个TCP连接，不同的浏览器有所区别。
![keep-alive](/assets/httpImg/keepAlive.png "keep-alive")

**我们关闭浏览器的长连接试试**
```js
//server.js
const img = fs.readFileSync('1.png');
res.writeHead(200, {
    'Content-Type': 'image/png',
    'Connection': 'close'
})
res.end(img);
```
![close](/assets/httpImg/close.png "close")
![close2](/assets/httpImg/close2.png "close2")
