---
title: HTTP报文
date: 2021-04-07 10:02:20
tog: http
toc: true
---

>目前网络应用都是用TCP/IP协议进行通信的。而在网络通信中使用最多的web协议就是基于TCP的HTTP协议，由此可见http的重要性。

### HTTP协议
HTTP全称是HyperText Transfer Protocol，即：超文本传输协议，从1990年开始就在WWW上广泛应用，是现今在WWW上应用最多的协议， Http是应用层协议，当你上网浏览网页的时候，浏览器和Web服务器之间就会通过HTTP在Internet上进行数据的发送和接收。

HTTP它是属于TCP上层的协议，但是本身并无会话的特点，它是一个基于请求/响应模式的、无状态的协议，以一问一答的方式实现服务。即我们通常所说的Request/Response。

那么TCP与HTTP协议两者之间究竟在网络通信中起到什么作用以及有什么联系呢？下面我们通过 一张图展示在浏览器上访问一个网页，各种协议在里面起的作用就能找到答案。
![http](/assets/httpImg/http.png "http")

### HTTP请求报文
一个请求报文有以下四个部分组成：`请求行（request line)`、`消息头部（header)` 、`空行` 、`请求正文` 

如下请求报文：
```js
POST /index.html HTTP/1.1
HOST: www.XXX.com
User-Agent: Mozilla/5.0(Windows NT 6.1;rv:15.0) Firefox/15.0

Username=admin&password=admin
```

#### 1、请求行
请求行由`请求方法`、`URL`和`HTTP协议版本`3个字段组成，它们用空格分隔。
例如：
```js
GET /index.html HTTP/1.1
```

HTTP协议的请求方法有:
* GET 获取资源
* POST 向服务器端发送数据，传输实体主体
* PUT 传输文件
* HEAD 获取报文首部
* DELETE 删除文件
* OPTIONS 询问支持的方法
* TRACE 追踪路径

#### 2、请求头部
请求头部由关键字/值对组成，每行一对，关键字和值用英文冒号`“:”`分隔。请求头部通知服务器有关于客户端请求的信息
* 通用首部(General Header)
* 请求首部(Request Header)
* 响应首部(Response Header)
* 实体首部(Entity Header Fields)
如下图：
![header](/assets/httpImg/header.png "header")

其中典型的请求头有：
```js
User-Agent：产生请求的浏览器类型。
Accept：客户端可识别的内容类型列表。
Host：请求的主机名，允许多个域名同处一个IP地址，即虚拟主机。
```

#### 3、空行
最后一个请求头之后是一个空行，发送回车符和换行符，通知服务器以下不再有请求头。

#### 4、请求数据
请求数据不在GET方法中使用，而是在POST方法中使用。POST方法适用于需要客户填写表单的场合。与请求数据相关的最常使用的请求头是Content-Type和Content-Length。

### HTTP响应报文
HTTP响应也由四个部分组成，分别是：`响应行`、`响应头`、`空行`、`响应体`.
如下响应报文所示：
```html
HTTP/1.1 200 OK
Content-Encoding: gzip
Content-Type: text/html;charset=utf-8

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>Document</title>
</head>
<body>
    <p>this is http response</p>
</body>
</html>
```
![response](/assets/httpImg/response.png "response");
正如你所见，在响应中唯一真正的区别在于第一行中用状态信息代替了请求信息。状态行（status line）通过提供一个状态码来说明所请求的资源情况。

#### 1、状态行
状态行 : HTTP-Version Status-Code Reason-Phrase CRLF
* HTTP-Version 服务器HTTP协议的版本
* Status-Code 服务器发回的响应状态代码
* Reason-Phrase 状态代码的文本描述

>状态码 ：状态码负责表示客户端请求的返回结果、标记服务器端是否正常、通知出现的错误。状态代码由三位数字组成，第一个数字定义了响应的类别，且有五种可能取值

**状态码**
* 状态代码有三个数字组成，第一个数字定义了响应的类别，且有五种可能取值：
* 1xx: 指示信息-表示请求已接收，继续处理
* 2xx: 成功-表示请求已被成功接收、理解、接受
* 3xx: 重定向-要完成请求必须进行更进一步的的操作
* 4xx: 客户端错误-请求有语法错误或请求无法实现
* 5xx: 服务器端错误-服务器未能实现合法的请求

常见状态代码、状态描述的说明如下。
* 200 OK：客户端请求成功。
* 400 Bad Request：客户端请求有语法错误，不能被服务器所理解。
* 401 Unauthorized：请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用。
* 403 Forbidden：服务器收到请求，但是拒绝提供服务。
* 404 Not Found：请求资源不存在，举个例子：输入了错误的URL。
* 500 Internal Server Error：服务器发生不可预期的错误。
* 503 Server Unavailable：服务器当前不能处理客户端的请求，一段时间后可能恢复正常，举个例子：HTTP/1.1 200 OK（CRLF）。

### HTTP首部
#### 通用首部字段
>Cache-Control 控制缓存行为
>Connection 链接的管理
>Date 报文日期
>Pragma 报文指令
>Trailer 报文尾部的首部
>Trasfer-Encoding 指定报文主体的传输编码方式
>Upgrade 升级为其他协议
>Via 代理服务器信息
>Warning 错误通知

#### 请求首部字段
>Accept 用户代理可处理的媒体类型
>Accept-Charset 优先的字符集
>Accept-Encoding Accept-Encoding
>Accept-Langulage 优先的语言
>Authorization Web认证信息
>Expect 期待服务器的特定行为
>From 用户的电子邮箱地址
>Host 请求资源所在的服务器
>If-Match 比较实体标记
>If-Modified-Since 比较资源的更新时间
>If-None-Match 比较实体标记
>If-Range 资源未更新时发送实体Byte的范围请求
>If-Unmodified-Since 比较资源的更新时间(和If-Modified-Since相反)
>Max-Forwards 最大传输跳数
>Proxy-Authorization 代理服务器需要客户端认证
>Range 实体字节范围请求
>Referer 请求中的URI的原始获取方
>TE 传输编码的优先级
>User-Agent HTTP客户端程序的信息
