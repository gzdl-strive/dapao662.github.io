---
title: CSP(内容安全策略)
date: 2021-04-11 15:49:20
tog: http
toc: true
---

>内容安全策略 (CSP) 是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括跨站脚本 (XSS) 和数据注入攻击等。无论是数据盗取、网站内容污染还是散发恶意软件，这些攻击都是主要的手段。

### 限制方式
* Content-Security-Policy: 'default-src'

### 资源类型
>`'Content-Security-Policy': 'script-src \'self\'; form-action \'self\'; report-uri / report`

### 示例1：
```html
<body>
    <script>
        console.log('123')
    </script>
    <script src='./hello.js'></script>
    <script src="https://www.baidu.com"></script>
</body>
```
```js
// server.js
const http = require('http');
const fs = require('fs');

http.createServer(function (req, res) {
    if (req.url === '/') {
        const html = fs.readFileSync('index.html', 'utf-8');
        res.writeHead(200, {
            'Content-Type': 'text/html',
            'Content-Security-Policy': 'default-src http: https:'
        });
        res.end(html);
    } else {
        //加载hello.js文件
        res.writeHead(200, {
            'Content-Type': 'text/javascript',
        });
        res.end('console.log("Simple rush b")');
    }
}).listen(8888);
console.log('server start at http://127.0.0.1:8888')
```
![csp2](/assets/httpImg/csp2.png "加载")
![csp1](/assets/httpImg/csp1.png "内联错误")
翻译如下：
>拒绝执行内联脚本，因为它违反了以下内容安全策略指令：`“default src http:https:”`。启用内联执行需要`'unsafe inline'`关键字、哈希（'sha256-xJp4geNUJc5rDVZYqTCUjlJPY+PLCwP17ykLBIxZjH8='）或nonce（'nonce-…'）。另外请注意，没有显式设置“script src”，因此使用“default src”作为回退。

**现在我们修改server.js内容=>我们不想让https/http加载，只想让hello.js加载，我们需要加self**
```js
// server.js
if (req.url === '/') {
    const html = fs.readFileSync('index.html', 'utf-8');
    res.writeHead(200, {
        'Content-Type': 'text/html',
        'Content-Security-Policy': 'default-src \'self\''
    });
    res.end(html);
}
```
![csp3](/assets/httpImg/csp3.png "只加载自己的脚本")
**我们这时候可以在self后再加上www.baidu.com就可以加载https://www.baidu.com了**
```js
//server.js
res.writeHead(200, {
    'Content-Type': 'text/html',
    'Content-Security-Policy': 'default-src \'self\' www.baidu.com'
});
```

### 示例2:report-uri / report
**我们来试试`report-uri / report`**
```js
//server.js
if (req.url === '/') {
    const html = fs.readFileSync('index.html', 'utf-8');
    res.writeHead(200, {
        'Content-Type': 'text/html',
        'Content-Security-Policy': 'default-src \'self\'; report-uri / report'
    });
    res.end(html);
}
```
![csp4](/assets/httpImg/csp4.png "两个report")
![csp5](/assets/httpImg/csp5.png "第一个report")
![csp6](/assets/httpImg/csp6.png "第二个report")

>不仅仅可以在服务器端设置，我们也可以在html中设置
```html
<!-- index.html -->
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; report-uri / report">
```
