---
title: 抓取天猫
date: 2020-12-29 10:02:19
tag: nodejs
---

>assert断言模块
```js
function sum(a,b) {
    assert(arguments.length==2,'必须传递两个参数');
    assert(typeof a=='number','a元素类型必须number');
    assert(typeof b=='number','b元素类型必须number');
    return a+b;
}
console.log(sum(14,15));
// console.log(sum(14,"ab"));------------>控制台报错
```

**使用断言模块来补充**
```js
const http = require('http');
const https = require('https');
const { resolve} = require('path');
const urlLib = require('url');
const fs = require('fs');
const assert = require('assert');

function requestUrl(url) {
    let urlObj = urlLib.parse(url);
    let httpMod = null;
    if (urlObj.protocol === 'http:') {
        httpMod = http;
    } else if (urlObj.protocol === 'https:') {
        httpMod = https;
    } else {
        throw new Error(`无法识别协议${urlObj.protocol}`);
    }
    return new Promise((resolve, reject) => {
        let req = httpMod.request({
            host: urlObj.host,
            path: urlObj.path,
        }, res => {
            if (res.statusCode >= 200 && res.statusCode < 300 || res.statusCode === 304) {
                let arr = [];
                res.on('data', data => {
                    arr.push(data);
                });
                res.on('end', () => {
                    let buffer = Buffer.concat(arr);
                    resolve({
                        status: 200,
                        body: buffer,
                        headers: res.headers
                    })
                })
            } else if (res.statusCode === 301 || res.statusCode === 302) {
                resolve({
                    status: res.statusCode,
                    body: null,
                    headers: res.headers
                })
            } else {
                reject({
                    status: res.statusCode,
                    body: null,
                    headers: res.headers
                })
            }
        })
        req.on('error', err => {
            console.log(`错误信息为${err}`);
        });
        req.write('');
        req.end();
    })
}

async function request(url) {
    try {
        while(1) {
            let {status, body, headers} = await requestUrl(url);
            /*
            302 null {
                server: 'Tengine',
                date: 'Mon, 28 Dec 2020 13:28:33 GMT',
                'content-type': 'text/html',
                'content-length': '258',
                connection: 'close',
                location: 'https://www.tmall.com/',
                via: 'cache6.cn1784[,0]',
                'timing-allow-origin': '*',
                eagleid: '73df1f9a16091621134262549e'
            }
            */
           if (status === 200) {
               return {status, body, headers};
           } else {
               assert(status === 301 || status === 302);
               assert(headers.location);
               url = headers.location;
           }
        }
    } catch(e) {
        console.log(e);
    }
}

const site = 'http://www.tmall.com';
(async (site) => {
    let {status, body, headers}= await request(site);
    fs.writeFile(resolve('build', 'tmall.html'), body, err => {
        if (err) {
            console.error(`写入文件错误:${err}`);
        } else {
            console.log('写入文件成功');
        }
    });
})(site);
```