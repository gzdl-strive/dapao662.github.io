---
title: Promise的基本使用
date: 2021-11-01 10:21:24
toc: true
---

## 1、初体验

```js
//生成随机数
function rand(m, n) {
  return Math.ceil(Math.random() * (n - m + 1)) + m - 1;
}
//resolve 解决 函数类型数据
//reject  拒绝 函数类型数据
const p = new Promise((resolve, reject) => {
  setTimeout(() => {
    let n = rand(1, 100);
    if (n <= 30) {
      resolve(n); //将promise对象状态设置为成功
    } else {
      reject(n); //将promise对象状态设置为失败
    }
  }, 1000);
});
//调用 then 方法
// value 值
// reason 理由
p.then(
  (value) => {
    alert("成功");
  },
  (reason) => {
    alert("失败");
  }
);
```

## 2、Promise 实践练习

### 2.1、fs 模块

```js
const fs = require("fs");

//回调函数形式
fs.readFile("./resource/content.txt", (err, data) => {
  //如果出错，抛出错误
  if (err) throw err;
  //成功，输出内容
  console.log(data.toString());
});

//Promise形式
let p = new Promise((resolve, reject) => {
  fs.readFile("./resource/content.txt", (err, data) => {
    //如果出错
    if (err) reject(err);
    //成功
    resolve(data);
  });
});
//调用then
p.then(
  (value) => {
    console.log(value.toString());
  },
  (reason) => {
    console.log(reason);
  }
);
```

### 2.2、AJAX 请求

```js
const p = new Promise((resolve, reject) => {
  //1、创建对象
  const xhr = new XMLHttpRequest();
  //2、初始化
  xhr.open("GET", "https://api.apiopen.top/getJoke");
  //3、发送
  xhr.send();
  //4、处理响应结果
  xhr.onreadystatechange = function () {
    if (xhr.readyState === 4) {
      //判断响应状态码 2xx
      if (xhr.status >= 200 && xhr.status < 300) {
        //成功输出响应体内容
        resolve(xhr.response);
      } else {
        //失败输出状态码
        reject(xhr.status);
      }
    }
  };
});

p.then(
  (value) => {
    console.log(value);
  },
  (reason) => {
    console.warn(reason);
  }
);
```

## 3、Promise 封装练习

### 3.1、fs 模块

```js
const fs = require("fs");
/**
 * 封装一个函数 mineReadFile 读取文件内容
 * 参数:  path  文件路径
 * 返回:  promise 对象
 */
function mineReadFile(path) {
  return new Promise((resolve, reject) => {
    fs.readFile(path, (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data);
      }
    });
  });
}
mineReadFile("./resource/content.txt").then(
  (value) => {
    console.log(value.toString());
  },
  (reason) => {
    console.log(reason);
  }
);
```

### 3.2、util.promisify

```js
//引入util模块
const util = require("util");
//引入fs
const fs = require("fs");
//返回一个新的函数
let mineReadFile = util.promisify(fs.readFile);

mineReadFile("./resource/content.txt").then((value) => {
  console.log(value.toString());
});
```

### 3.3、封装 AJAX 操作

```js
/**
 * 封装一个函数 sendAJAX 发送 GET AJAX 请求
 * 参数   URL
 * 返回结果 Promise 对象
 */
function sendAJAX(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.responseType = "json";
    xhr.open("GET", url);
    xhr.send();
    //处理结果
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve(xhr.response);
        } else {
          reject(xhr.status);
        }
      }
    };
  });
}
sendAJAX("https://api.apiopen.top/getJoke").then(
  (value) => {
    console.log(value);
  },
  (reason) => {
    console.warn(resons);
  }
);
```
