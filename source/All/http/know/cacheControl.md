---
title: Cache-Control
date: 2021-04-10 21:59:20
tog: http
toc: true
---

### 可缓存性
* public 任何都可以
* private 只有它发起浏览器可以缓存
* no-cache 去服务端验证才能使用
* no-store 彻底不能
* no-transform 代理服务器不能改动返回内容

### 到期时间(seconds)
* max-age = <seconds> 最大时间
* s-maxage = <seconds> 只有代理服务器才会生效
* max-stale = <seconds> 只能在发起端设置 就算max-age时间过期 max-stale时间没过期也会走缓存

### 重新验证(了解)
* must-revalidate
* proxy-revalidate

### 验证头(不走本地缓存 发送请求带上验证头 验证决定走不走缓存 它基于no-cache的情况)
* last-Modified
    * 配合if-Modified-Since或者if-Unmodified-Since使用
    * 对比上次修改时间以验证资源是否需要更新
* Etag
    * 数据签名(哈希值)
    * 配合if-Match或者if-Non-Match使用
    * 对比资源的签名判断是否使用缓存

### 示例：
先设置谷歌浏览器的F12控制台的NetWork模块的`Disable cache`(取消选中)
![disableCache](/assets/httpImg/disableCache.png "disableCache")

### 示例1:验证(到期时间)max-age
>max-age是HTTP/1.1中,他是指我们的web中的文件被用户访问(请求)后的存活时间,是个相对的值,相对Request_time(请求时间).例如:A.html  用户请求时间是18:00,max-age设置的是600的话,相当18:00+600秒过期,也就是相对18:00的时间后面600秒后过期.默认的max-age是由Expires算出来的.
```html
<!-- index.html -->
<body>
    <h1>hello</h1>
    <script src="./hello.js"></script>
</body>
```
**这里我们设置max-age=100（100秒）**
```js
// server.js
const http = require('http');
const fs = require('fs');
http.createServer(function (req, res) {
    if (req.url === '/') {
        const html = fs.readFileSync('index.html', 'utf-8');
        res.writeHead(200, {
            'Content-Type': 'text/html'
        });
        res.end(html);
    }
    if (req.url === '/hello.js') {
        res.writeHead(304, {
            'Content-Type': 'text/javascript',
            'Cache-Control': 'max-age=100',
        })
        res.end('console.log("Simple rush B")')
    }
}).listen(8888);
console.log('server start at http://127.0.0.1:8888')
```
![max-age](/assets/httpImg/maxAge.png "max-age")
如果我们这时候修改console.log里面的内容，那么刷新浏览器也不会改变
例如我们修改server.js中res.end内容:
```js
// server.js
res.end('console.log("Simple rush B222")')
```
这时候刷新网页还是之前的内容!

### 示例2:验证-可缓存性(no-cache)和验证头Etag(配合if-Non-Match使用)
```js
//server.js
const http = require('http');
const fs = require('fs');
http.createServer(function (req, res) {
    if (req.url === '/') {
        const html = fs.readFileSync('index.html', 'utf-8');
        res.writeHead(200, {
            'Content-Type': 'text/html'
        });
        res.end(html);
    }
    if (req.url === '/hello.js') {
        const etag = req.headers['if-none-match'];
        console.log(etag);
        if (etag === '1308252') {
            res.writeHead(304, {
                'Content-Type': 'text/javascript',
                'Cache-Control': 'max-age=3000,no-cache',
                'Etag': '1308252'
            })
        } else {
            res.writeHead(200, {
                'Content-Type': 'text/javascript',
                'Cache-Control': 'max-age=3000,no-cache',
                'Etag': '1308252'
            })
        }
        res.end('console.log("Simple rush B")')
    }
}).listen(8888);
console.log('server start at http://127.0.0.1:8888')
```
因为Cache-Control设置了no-cache,所以会去服务端验证才能使用
我们这边验证头使用Etag,设置了一个值
**第一次访问：`const etag = req.headers['if-none-match'];console.log(etag);` => 打印出`undefined`**
![noCacheEtag](/assets/httpImg/noCacheEtag1.png "noCacheEtag")
**第二次访问(刷新一下):`console.log(etag);` => 打印出`1308252`**
![noCacheEtag2](/assets/httpImg/noCacheEtag2.png "noCacheEtag2")
**第三次访问(刷新一下):`console.log(etag);` => 打印出`1308252`**
...
