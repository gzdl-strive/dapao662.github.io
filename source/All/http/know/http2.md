---
title: HTTP2
date: 2021-04-12 09:19:20
tog: http
toc: true
---


![http2](/assets/httpImg/http2.png "http2")
### 二进制协议
* http1.1头信息必须是字符，数据体可以是文本，也可以是二进制
* http2全部都是二进制

### 头信息压缩
* 头部信息一样的变相缓存

### 信道复用(多路复用)
* 同一个连接里面发送多个请求不再需要按照顺序来(解决了http1.1里面的缺点：队头阻塞)

### 数据流
* http1.1版本取消数据流的唯一方法，就是关闭TCP连接
* http2可以取消某一次连接，同时保证TCP连接还打开着，可以被其他请求使用

### 服务器推送
* http2服务端可以推送，主动向客户端发送请求
>http1解析html页面里面css、js文件，需要解析到css和js文件发送请求等数据回来渲染
>http2解析html页面里面建立连接，如果css、js文件，服务端会主动推送到客户端，不再发送请求获取css、js文件
* 浏览器目前是必须开启https才能使用http2

HTTP和HTTP2对比演示的DEMO地址:[https://http2.akamai.com/demo](https://http2.akamai.com/demo "demo")