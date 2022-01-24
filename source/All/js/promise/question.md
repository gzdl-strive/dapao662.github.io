---
title: Promise几个关键问题
date: 2021-11-01 11:02:24
toc: true
---

## 1、如何修改 Promise 对象状态

```js
let p = new Promise((resolve, reject) => {
  //1. resolve 函数
  // resolve('ok'); // pending   => fulfilled (resolved)
  //2. reject 函数
  // reject("error");// pending  =>  rejected
  //3. 抛出错误
  // throw '出问题了';
});
```

> resolve -> (pending -> fullfilled)
> reject -> (pending -> rejected)
> throw -> (pending -> rejected)

### 2、能否执行多个回调

```js
let p = new Promise((resolve, reject) => {
  resolve("OK");
});

///指定回调 - 1
p.then((value) => {
  console.log(value);
});

//指定回调 - 2
p.then((value) => {
  alert(value);
});
```

可以执行多个回调

### 3、改变状态与指定回调顺序问题

```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("OK");
  }, 1000);
});

p.then(
  (value) => {
    console.log(value);
  },
  (reason) => {}
);
```

### 4、then 方法的返回结果由什么决定

```js
let p = new Promise((resolve, reject) => {
  resolve("ok");
});
//执行 then 方法
let result = p.then(
  (value) => {
    // console.log(value);
    //1. 抛出错误
    // throw '出了问题';
    //2. 返回结果是非 Promise 类型的对象
    // return 521;
    //3. 返回结果是 Promise 对象
    // return new Promise((resolve, reject) => {
    //     // resolve('success');
    //     reject('error');
    // });
  },
  (reason) => {
    console.warn(reason);
  }
);

console.log(result);
```

### 5、promise 如何串联多个任务

```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("OK");
  }, 1000);
});

p.then((value) => {
  return new Promise((resolve, reject) => {
    resolve("success");
  });
})
  .then((value) => {
    console.log(value);
  })
  .then((value) => {
    console.log(value);
  });
```

### 6、异常穿透

```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("OK");
    // reject('Err');
  }, 1000);
});

p.then((value) => {
  // console.log(111);
  throw "失败啦!";
})
  .then((value) => {
    console.log(222);
  })
  .then((value) => {
    console.log(333);
  })
  .catch((reason) => {
    console.warn(reason);
  });
```

### 7、如何中断 Promise 链条

```js
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("OK");
  }, 1000);
});

p.then((value) => {
  console.log(111);
  //有且只有一个方式
  return new Promise(() => {});
})
  .then((value) => {
    console.log(222);
  })
  .then((value) => {
    console.log(333);
  })
  .catch((reason) => {
    console.warn(reason);
  });
```
