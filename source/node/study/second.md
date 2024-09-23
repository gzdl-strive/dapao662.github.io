---
title: web服务器
date: 2020-11-23 20:05:19
tag: nodejs
---

## web服务器

``` js
//引入http模块
const http = require('http');
//引入url模块
const url = require('url');
/**
 * 创建一个服务
 * req => 客户端发送过来的消息对象,理解为 请求对象
 * res => 响应对象，将要响应给客户端的对象
 */
const server = http.createServer((req, res) => {
    //对客户端发来的请求进行处理
    //封装将要响应回客户端的数据
    //发送响应状态
    res.statusCode = 200;
    //设置响应头（响应数据类型及编码）
    res.setHeader('Content-Type', 'text/html;charset=utf-8');

    //获取客户端请求方式
    console.log(req.method);

    //获取url对象,将url转成对象，并将查询字符串转成对象
    //第一种获取get请求参数的方式，在parse后加一个true参数
    let myUrl = url.parse('http://127.0.0.1:3000' + req.url, true);
    console.log(myUrl);
    let id = myUrl.query.id;
    console.log(id);

    //第二种获取get请求参数的方式
    let myUrl = url.parse('http://127.0.0.1:3000' + req.url);
    let _params = new URLSearchParams(myUrl.query);
    if (_params.has('id')) {
        let id = _params.get('id');
        console.log(id);
    } else {
        console.log('没有找到id参数');
    }

    //获取post请求参数
    /**
     * data事件：表示有数据通过POST请求传输过来
     * POST请求，数据量会非常大，所以发送会分批次发送
     */
    let params = ''; //如果数据量大--》分批次，可以赋值给params
    req.on('data', (chunk) => {
        //console.log(chunk.toString());
        params += chunk;
    });
    /**
     * end事件表示：全部的POST请求数据均已接收
     * 已经结束
     */
    req.on('end', () => {
        let queryString = params.toString();
        let _param = new url.URLSerachParams(queryString);
        let username = _param.get('username');
        let password = _param.get('password');
        console.log(`账号：${username}密码:${password}结束`);
    })；

    //响应结束，发送消息
    res.end('Hello(你好) Node.js')//中文乱码--设置请求头
})；

//监听服务
server.listen(3000, '127.0.0.1', () => {
    console.log('服务器运行在：http://127.0.0.1:3000')
})
```
