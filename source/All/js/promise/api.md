---
title: Promise的API
date: 2021-11-01 10:55:24
toc: true
---

> then / catch
> resolve / reject
> all
> race

## then 和 catch 方法

> then：链式操作，返回一个新的 Promise 对象
> catch: 是 then(null, rejection)的别名，用于指定发生错误时的回调函数

```js
let p = new Promise((resolve, reject) => {
  // ** 同步调用
  //修改 promise 对象的状态
  reject("error");
});
//执行 catch 方法
p.catch((reason) => {
  console.log(reason);
});
```

## resolve 和 reject

```js
let p1 = Promise.resolve(521);
//如果传入的参数为 非Promise类型的对象, 则返回的结果为成功promise对象
//如果传入的参数为 Promise 对象, 则参数的结果决定了 resolve 的结果
let p2 = Promise.resolve(
  new Promise((resolve, reject) => {
    // resolve('OK');
    reject("Error");
  })
);
// console.log(p2);
p2.catch((reason) => {
  console.log(reason);
});
```

```js
// let p = Promise.reject(521);
// let p2 = Promise.reject('iloveyou');
let p3 = Promise.reject(
  new Promise((resolve, reject) => {
    resolve("OK");
  })
);

console.log(p3);
```

## all 方法

> Promise.all 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```js
let p1 = new Promise((resolve, reject) => {
  resolve("OK");
});
let p2 = Promise.resolve("Success");
let p2 = Promise.reject("Error");
let p3 = Promise.resolve("Oh Yeah");

//
const result = Promise.all([p1, p2, p3]);

console.log(result);
```

## race 方法

> Promise.race 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。（只有有一个实例率先改变状态，那么该返回 Promise 状态就跟着改变）

```js
let p1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("OK");
  }, 1000);
});
let p2 = Promise.resolve("Success");
let p3 = Promise.resolve("Oh Yeah");

//调用
const result = Promise.race([p1, p2, p3]);

console.log(result);
```
