---
title: Cookie
date: 2021-04-11 13:26:20
tog: http
toc: true
---


![cookie](/assets/httpImg/cookie.png "Cookie");

### 用法
* 通过Set-Cookie设置
* 下次请求会自动带上
* 键值对，可以设置多个

### 属性
* max-age和expires设置过期时间
* Secure只在https的时候发送
* HttpOnly无法通过document.cookie访问

### 示例1:服务端Set-Cookie
```html
<!-- index.html -->
    <script>
        console.log(document.cookie)
    </script>
```
```js
const http = require('http');
const fs = require('fs');

http.createServer(function(req, res) {
    const html = fs.readFileSync('index.html', 'utf-8');
    res.writeHead(200, {
        'Content-Type': 'text/html',
        'Set-cookie': ['id=123', 'title=456']
    });
    res.end(html);
}).listen(8888);
console.log('server start at http://127.0.0.1:8888')
```
![cookieHtml](/assets/httpImg/cookieHtml.png "CookieHtml");
![setCookie](/assets/httpImg/cookieSetCookie.png "setCookie");

### 示例2：max-age属性
我们修改server.js中`Set-Cookie`的内容，加上`max-age`属性
```js
res.writeHead(200, {
    'Content-Type': 'text/html',
    'Set-cookie': ['id=123;max-age=2', 'title=456'];//id=123的cookie的max-age为2秒
});
```
![max-age](/assets/httpImg/cookieMaxAge1.png "max-age");
**两秒后再刷新页面**
![max-age2](/assets/httpImg/cookieMaxAge2.png "max-age");

### 示例3: HttpOnly属性
我们修改server.js中`Set-Cookie`的内容，加上`HttpOnly`属性
```js
res.writeHead(200, {
    'Content-Type': 'text/html',
    'Set-cookie': ['id=123;max-age=2', 'title=456;HttpOnly'];//id=123的cookie的max-age为2秒,title=456的cookie不能被document.cookie访问
});
```
![httpOnly](/assets/httpImg/cookieHttpOnly.png "httpOnly");
![httpOnly2](/assets/httpImg/cookieHttpOnly2.png "httpOnly");
