---
title: ECMAScript 8 新特性
date: 2021-11-16 10:14:02
toc: true
---

## async 和 await

async 和 await 两种语法结合可以让异步代码像同步代码一样

### 1、async

1. async 函数的返回值为 promise 对象，
2. promise 对象的结果由 async 函数执行的返回值决定

```js
//async 函数
async function fn() {
  // 返回一个字符串
  // return '尚硅谷';
  // 返回的结果不是一个 Promise 类型的对象, 返回的结果就是成功 Promise 对象
  // return;
  //抛出错误, 返回的结果是一个失败的 Promise
  // throw new Error('出错啦!');
  //返回的结果如果是一个 Promise 对象
  return new Promise((resolve, reject) => {
    resolve("成功的数据");
    // reject("失败的错误");
  });
}

const result = fn();

//调用 then 方法
result.then(
  (value) => {
    console.log(value);
  },
  (reason) => {
    console.warn(reason);
  }
);
```

### 2、await

1. await 必须写在 async 函数中
2. await 右侧的表达式一般为 promise 对象
3. await 返回的是 promise 成功的值
4. await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理

```js
//创建 promise 对象
const p = new Promise((resolve, reject) => {
  // resolve("用户数据");
  reject("失败啦!");
});

// await 要放在 async 函数中.
async function main() {
  try {
    let result = await p;
    //
    console.log(result);
  } catch (e) {
    console.log(e);
  }
}
//调用函数
main();
```

## Object.values 和 Object.entries

1. Object.values()方法返回一个给定对象的所有可枚举属性值的数组
2. Object.entries()方法返回一个给定对象自身可遍历属性 [key,value] 的数组

## Object.getOwnPropertyDescriptors

该方法返回指定对象所有自身属性的描述对象
