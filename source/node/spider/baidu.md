---
title: 抓取百度
date: 2020-12-29 09:05:19
tag: nodejs
---


**`let req = http.request(options, callback);`**
* options-->用于请求的选项
* callback-->处理响应的回调函数

**`path.resolve([from ...], to)`**
>将` to `参数解析为绝对路径，给定的路径的序列是从右往左被处理的，后面每个` path `被依次解析，直到构造完成一个绝对路径。例如，给定的路径片段的序列为：`/foo、/bar、baz`，则调用 `path.resolve('/foo', '/bar', 'baz') `会返回` /bar/baz`。

**写入文件-fs.writeFile(file, data[, options], callback)**
>`writeFile `直接打开文件默认是` w 模式`，所以如果文件存在，该方法写入的内容会覆盖旧的文件内容
* file - 文件名或文件描述符
* data - 要写入文件的数据，可以是 String(字符串) 或 Buffer(缓冲) 对象
* options - 该参数是一个对象，包含 {encoding, mode, flag}。默认编码为 utf8, 模式为 0666 ， flag 为 'w'
* callback - 回调函数，回调函数只包含错误信息参数(err)，在写入失败时返回。

```js
const { resolve } = require('path');
const http = require('http');
const fs = require('fs');

let req = http.request('http://www.baidu.com', res => {
    // console.log(res.statusCode);200
    if (res.statusCode >= 200 && res.statusCode < 300 || res.statusCode = 304) {
        let arr = [];
        res.on('data', data => {
            arr.push(data);
        });
        res.on('end', () => {
            let buffer = Buffer.concat(arr);
            fs.writeFile(resolve('build', 'baidu.html'), buffer, err => {
                if (err) {
                    console.log('写入异常', err);
                } else {
                    console.log('写入成功');
                }
            });
            console.log('完成', buffer);
        })
    } else {
        console.log('错误,错误状态码为:', res.statusCode);
    }
});
req.on('error', err => {
    console.log('错误信息为:', err);
});
req.write('');//发送POST数据
req.end();
```

**终端中运行node baidu.js会在build目录下生成baidu.html-->如果没有build目录请添加build目录或修改写入文件代码**