---
title: nginx简单配置
date: 2021-04-13 10:57:20
tog: http
toc: true
---


### 下载
>[nginx下载](http://nginx.org/en/download.html "nginx下载")
>下载到知道文件夹下，进入当前文件夹
启动方法一：直接点击nginx.exe
启动方法二：start nginx
停止：nginx -s stop ==>Stop 是快速关闭，不管有没有正在处理的请求。
优雅的停止方法：nginx -s quit ==>Quit 是一个优雅的关闭方式，Nginx在退出前完成已经接受的连接请求。
重启: nginx -s reload
**下载完成后访问`http://localhost`可以看到welcom nginx页面**

### 练习
>我们可把conf文件夹下的nginx.conf文件修改，以完成更多的应用,nginx.conf是nginx的配置文件
>首先我们应把其他文件放到单独的文件，不要都写在nginx.conf中，这样会显得很乱
>我们可以在nginx.conf同级目录下建立一个servers的目录，用于存放我们测试的conf文件
>我们在nginx.conf中应该做的操作是：注释掉默认的server对象，因为我们要在另外的conf文件中写这个对象
>在http这整个对象的后面加上`include servers/*.conf;`表示引入servers文件夹下的所有.conf文件
```conf
#user  nobody;
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    #...
    sendfile        on;
    #tcp_nopush     on;
    #keepalive_timeout  0;
    keepalive_timeout  65;
    #gzip  on;
    # server {
    #     listen       8081;
    #     server_name  localhost;
    #     #charset koi8-r;
    #     #access_log  logs/host.access.log  main;
    #     location / {
    #         root   html;
    #         index  index.html index.htm;
    #     }                                     
    include servers/*.conf;#新增
}
```
**然后我们在servers目录下新建一个test.conf文件**
```conf
# test.conf
server {
    listen 8081;#监听8081端口
    server_name www.test.com;

    location / {
        proxy_pass http://127.0.0.1:8888;#转发http://127.0.0.:8888
    }
}
```
这时候我们启动一个本地服务http://127.0.0.1:8888,只要我们访问http://www.test.com:8081=》nginx就会帮我们代理http://127.0.0.1:8888
**本地服务如下**
```js
// server.js
const http = require('http');
const fs = require('fs');
http.createServer(function (req, res) {
    console.log(req.headers.host);
    if (req.url === '/') {
        const html = fs.readFileSync('index.html', 'utf-8');
        res.writeHead(200, {
            'Content-Type': 'text/html',
        });
        res.end(html);
    }
}).listen(8888);
console.log('server start at http://127.0.0.1:8888')
```

**这时候出现错误了**
>我们server_name后面跟着localhost就是正常的，可是跟着www.test.com就会报错
>解决方法：window下：进入C:\Windows\System32\drivers\etc下hosts文件,增加`127.0.0.1 www.test.com`
```conf
# Copyright (c) 1993-2009 Microsoft Corp.
#...
# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
127.0.0.1 www.test.com  #新增
```

**我们在本地服务器中打印了req.headers.host**
我们可以在终端中看到
```js
127.0.0.1:8888
127.0.0.1:8888
```
这是我们服务器实际的地址，但是我们不想让别人看到
所以我们需要在test.conf文件中新增配置
```conf
# test.conf
server {
    listen 8081;#监听8081端口
    server_name www.test.com;

    location / {
        proxy_pass http://127.0.0.1:8888;#转发http://127.0.0.:8888
        proxy_set_header Host $host;   #新增
    }
}
```
每次修改完nginx的配置，我们都需要重新启动nginx
在nginx目录下输入`nginx -s reload`即可

**这时我们就可以在终端中看到,req.headers.host打印出来的是**
```js
www.test.com
www.test.com
```

