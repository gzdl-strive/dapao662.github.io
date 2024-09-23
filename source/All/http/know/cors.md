---
title: 跨域CORS
date: 2021-04-08 09:18:20
tog: http
toc: true
---

### 1、什么是跨域
* 浏览器的同源策略限制了跨域请求资源
例如：我们使用node建立一个本地服务器，端口是8888
该服务器会读取同级目录下的index.html文件
```js
const http = require('http');
const fs = require('fs');

http.createServer(function (req, res) {
    console.log('server-URL:' + req.url);
    const html = fs.readFileSync('index.html', 'utf-8');
    res.writeHead(200, {
        'Content-type': 'text/html'
    });
    res.end(html);
}).listen(8888);
```
再通过node建立一个端口号为8886的本地服务器
```js
const http = require('http');

http.createServer((req, res) => {
    res.end('123');
}).listen(8886);
```
```html
<!-- index.html  -->
<body>
    <h1>hello</h1>
    <div style="width: 200px; height: 200px; background-color: red"></div>
    <script>
        console.log('ajax----')
        const xhr = new XMLHttpRequest();
        xhr.open('GET', 'http://127.0.0.1:8886');
        xhr.send();
    </script>
</body>
```

**index.html访问不同端口号的服务器，会报错，这就是跨域**
![cors](/assets/httpImg/cors.png "跨域")

>解决方法：我们需要在被访问的服务器中设置允许跨域
```js
const http = require('http');

http.createServer((req, res) => {
    res.writeHead(200, {
        'Access-Control-Allow-Origin': '*',
    })
    res.end('123');
}).listen(8886);
```

### 2、jsonp跨域

### 3、跨域的限制(预请求)
#### 默认跨域允许的方法只有get、post、head,其他的方法不允许
例如：我们在index.html请求PUT方法
```html
<script>
    console.log('ajax----')
    const xhr = new XMLHttpRequest();
    xhr.open('PUT', 'http://127.0.0.1:8886');
    xhr.send();
</script>
```
![method](/assets/httpImg/method.png "默认跨域方法")
>解决方法：我们需要在被访问的服务器中设置允许跨域的方法
```js
const http = require('http');

http.createServer((req, res) => {
    res.writeHead(200, {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'PUT,DELETE'//设置通过请求的方法
    })
    res.end('123');
}).listen(8886);
```

#### 请求头限制，自定义的请求头是不允许，预请求验证通过才能发送
例如：自定义请求头X-Test-Cors
```html
<script>
    fetch('http://127.0.0.1:8886', {
        method: 'PUT',
        headers: {
            'X-Test-Cors': 'HJM'
        }
    })
</script>
```
![test](/assets/httpImg/test.png "自定义请求头")
>解决方法：我们需要设置通过自定义的请求头
```js
const http = require('http');

http.createServer((req, res) => {
    res.writeHead(200, {
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'PUT,DELETE',
        'Access-Control-Allow-Headers': 'X-Test-Cors',
    })
    res.end('123');
}).listen(8886);
```

