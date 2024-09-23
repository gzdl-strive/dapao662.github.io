---
title: js笔记
date: 2021-01-22 14:52:30
tag: js
---

## 浏览器打开新窗口，当关闭窗口时刷新原窗口
```js
// a.html
window.open('http://www.baidu.com');
//当新窗口baidu关闭时->刷新原窗口
window.opener.location.reload(); self.close();
```

## jQuery.ajax请求报错415或400解决方案
>后台需要post请求，且在body中传参，格式为JSON
```js
function getData() {
    const {A, B, C} = this.state;
    $.ajax({
        type: 'post',
        dataType: 'json',
        url: 'xxxxx/xxxx',
        data: {
            "A": A,
            "B": B,
            "C": C
        },
        beforeSend: function () {
            //...
        },
        complete: function() {
            //...
        },
        success: function (res) {
            //...
        }
    })
}
```
>此时请求后台接口，就报415错误
>查看HTTP状态码可知415为：`415 Unsupported Media Type `服务器无法处理请求附带的媒体格式 (不支持的媒体类型)
>基本上就是跟 `content-type` 有关 (content-type：设置发送给服务器数据的格式)

**post提交数据四种常见的content-type取值**
>1.`application/x-www-form-urlencoded` ：最常见的 POST 提交数据的方式
>2.`multipart/form-data` ：使用表单上传文件时，传递这个值
>3.`application/json` ：用来告诉服务端发送的数据是序列化后的 JSON 字符串
4.`text/xml` ：使用 HTTP 作为传输协议，XML 作为编码方式的远程调用规范

>所以不难发现为什么默认的是 `application/x-www-form-urlencoded`

**也知道我们该用哪一个值了**
**初次修改**
```js
function getData() {
    const {A, B, C} = this.state;
    $.ajax({
        type: 'post',
        dataType: 'json',

        /*新增*/
        contentType: 'application/json;charset=UTF-8',

        url: 'xxxxx/xxxx',
        data: {
            "A": A,
            "B": B,
            "C": C
        },
        beforeSend: function () {
            //...
        },
        complete: function() {
            //...
        },
        success: function (res) {
            //...
        }
    })
}
```
**但还是报错了,报400**
>contentType已经修改过，为什么报400呢？
>因为前后台传入的数据不匹配，后台无法解析数据，告诉服务端数据是序列化后的 JSON 字符串，但是JSON数据确没有序列化或是没有满足JSON的格式。

>所以需要对json进行序列化 ==>`JSON.stringify( )`
**再次修改**
```js
function getData() {
    const {A, B, C} = this.state;
    $.ajax({
        type: 'post',
        dataType: 'json',

        contentType: 'application/json;charset=UTF-8',

        url: 'xxxxx/xxxx',

        /*新增*/
        data: JSON.stringify({
            "A": A,
            "B": B,
            "C": C
        }),
        beforeSend: function () {
            //...
        },
        complete: function() {
            //...
        },
        success: function (res) {
            //...
        }
    })
}
```
**这次，请求成功**