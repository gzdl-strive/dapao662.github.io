---
title: async/await
date: 2021-11-01 14:37:24
toc: true
---

**async**

```js
//和then返回的类似
async function main() {
  //1. 如果返回值是一个非Promise类型的数据
  // return 521;
  //2. 如果返回的是一个Promise对象
  // return new Promise((resolve, reject) => {
  //     // resolve('OK');
  //     reject('Error');
  // });
  //3. 抛出异常
  throw "Oh NO";
}
let result = main(); //该返回值是一个Promise对象
```

**await**

```js
async function main() {
  let p = new Promise((resolve, reject) => {
    // resolve('OK');
    reject("Error");
  });
  //1. 右侧为promise的情况
  // let res = await p;
  //2. 右侧为其他类型的数据
  // let res2 = await 20;
  //3. 如果promise是失败的状态
  try {
    let res3 = await p;
  } catch (e) {
    console.log(e);
  }
}

main();
```

**async 与 await 结合**

```js
/**
 * resource  1.html  2.html 3.html 文件内容
 */

const fs = require("fs");
const util = require("util");
const mineReadFile = util.promisify(fs.readFile);

//回调函数的方式
// fs.readFile('./resource/1.html', (err, data1) => {
//     if(err) throw err;
//     fs.readFile('./resource/2.html', (err, data2) => {
//         if(err) throw err;
//         fs.readFile('./resource/3.html', (err, data3) => {
//             if(err) throw err;
//             console.log(data1 + data2 + data3);
//         });
//     });
// });

//async 与 await
async function main() {
  try {
    //读取第一个文件的内容
    let data1 = await mineReadFile("./resource/1x.html");
    let data2 = await mineReadFile("./resource/2.html");
    let data3 = await mineReadFile("./resource/3.html");
    console.log(data1 + data2 + data3);
  } catch (e) {
    console.log(e.code);
  }
}

main();
```

**async 和 await 发生 AJAX 请求**

```js
function sendAJAX(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.responseType = "json";
    xhr.open("GET", url);
    xhr.send();
    //处理结果
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        //判断成功
        if (xhr.status >= 200 && xhr.status < 300) {
          //成功的结果
          resolve(xhr.response);
        } else {
          reject(xhr.status);
        }
      }
    };
  });
}
async function main() {
  //获取段子信息
  let duanzi = await sendAJAX("https://api.apiopen.top/getJoke");
  console.log(duanzi);
}
main();
```
