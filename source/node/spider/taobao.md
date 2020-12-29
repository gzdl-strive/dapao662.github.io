---
title: 抓取淘宝
date: 2020-12-29 09:44:19
tag: nodejs
---

>如果使用和抓取百度一样的代码抓取淘宝，不会自动生成taobao.html文件，因为淘宝使用的协议是https

```js
const http = require('http');
const { resolve } = require('path');
const fs = require('fs');

let req = http.request('http://www.taobao.com', (res) => {
    if (res.statusCode >=200 && res.statusCode <300 || res.statusCode === 304) {
        let arr = [];
        res.on('data', data => {
            arr.push(data);
        });
        res.on('end', () => {
            let buffer = Buffer.concat(arr);
            fs.writeFile(resolve('build', 'taobao.html'), buffer, (err) => {
                if (err) {
                    console.error('写入文件失败', err);
                } else {
                    console.log('写入成功');
                }
            });
        })
    } else if (res.statusCode === 301 || res.statusCode === 302){
        console.log(res.statusCode);//301
        console.log(res.headers);//淘宝使用https，从res.headers.location可以看出
        /*
            {
            server: 'Tengine',
            date: 'Mon, 28 Dec 2020 08:22:11 GMT',
            'content-type': 'text/html',
            'content-length': '278',
            connection: 'close',
            location: 'https://www.taobao.com/',  via: 'cache16.cn1447[,0]',
            'timing-allow-origin': '*',
            eagleid: '6f034fa416091437313074587e'
            }
        */
    } else {
        console.log('错误，状态码为:',res.statusCode);
    }
});
req.on('error', err => {
    console.log('错误', err);
})
req.write('');
req.end();
```

**改进**

```js
const ulrLib = require('url');
const { resolve } = require('path');
const http = require('http');
const https = require('https');
const fs = require('fs');

function requestUrl(url, headers) {
    let urlObj = urlLib.parse(url);
    let httpMod = null;
    if (urlObj.protocol === 'http:') {
        httpMod = http;
    } else if (urlObj.protocol === 'https:') {
        httpMod = https;
    } else {
        throw new Error(`${urlObj.protocol}-协议无法识别`)
    }
    return new Promise((resolve, reject) => {
        let req = httpMod.request({
            host: urlObj.host,
            path: urlObj.path,
            headers
        }, res => {
            if (res.statusCode >= 200 && res.status < 300 || res.statusCode === 304) {
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
                    });
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
        });
        req.on('error', err => {
            console.log('错误', err);
        });
        req.write('');
        req.end();
    })
}

async function request(url, reqheaders) {
    try {
        let {status, body, headers} = await requestUrl(url, reqheaders);
        /*
        301 null {
        server: 'Tengine',
        date: 'Mon, 28 Dec 2020 13:06:37 GMT',
        'content-type': 'text/html',
        'content-length': '278',
        connection: 'close',
        location: 'https://www.taobao.com/',  
        via: 'cache7.cn1410[,0]',
        'timing-allow-origin': '*',
        eagleid: 'b788879b16091607976463915e' 
        }
        */
        if (status === 200) {
            return {body, headers};
        } else {
           let {status, body, headers: headers2} = await requestUrl(headers.location, reqheaders);
           fs.writeFile(resolve('build', 'taobao.html'), body, err => {
               if (err) {
                   console.error(`写入文件失败:${err}`);
               } else {
                   console.log('写入文件成功');
               }
           });
        }
    } catch(e) {
        console.log('错误信息为：',e);
    }
}
const site = 'http://www.taobao.com';
request(site);
```