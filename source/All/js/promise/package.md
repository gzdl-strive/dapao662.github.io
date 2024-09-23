---
title: Promise的自定义封装
date: 2021-11-01 11:12:24
toc: true
---

### 1、初始结构搭建

```js
//声明构造函数
function Promise(executor) {
  //resolve 函数
  function resolve(data) {}
  //reject 函数
  function reject(data) {}

  //同步调用『执行器函数』
  executor(resolve, reject);
}

//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {};
```

### 2、resolve 和 reject 实现,以及 throw 抛出错误改变状态

```js
//声明构造函数
function Promise(executor) {
  //添加属性
  this.PromiseState = "pending";
  this.PromiseResult = null;
  //保存实例对象的 this 的值
  const self = this; // self _this that
  //resolve 函数
  function resolve(data) {
    //1. 修改对象的状态 (promiseState)
    self.PromiseState = "fulfilled"; // resolved
    //2. 设置对象结果值 (promiseResult)
    self.PromiseResult = data;
  }
  //reject 函数
  function reject(data) {
    //1. 修改对象的状态 (promiseState)
    self.PromiseState = "rejected"; //
    //2. 设置对象结果值 (promiseResult)
    self.PromiseResult = data;
  }

  try {
    //同步调用『执行器函数』
    executor(resolve, reject);
  } catch (e) {
    //修改 promise 对象状态为『失败』
    reject(e);
  }
}

//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {};
```

### 3、状态只能修改一次，then 方法执行回调，异步任务 then 方法执行回调，指定多个回调

```js
//声明构造函数
function Promise(executor) {
  //添加属性
  this.PromiseState = "pending";
  this.PromiseResult = null;
  //声明属性
  this.callback = [];
  //保存实例对象的 this 的值
  const self = this; // self _this that
  //resolve 函数
  function resolve(data) {
    //判断状态(状态只能修改一次)
    if (self.PromiseState !== "pending") return;
    //1. 修改对象的状态 (promiseState)
    self.PromiseState = "fulfilled"; // resolved
    //2. 设置对象结果值 (promiseResult)
    self.PromiseResult = data;
    //调用成功的回调函数
    self.callbacks.forEach((item) => {
      item.onResolved(data);
    });
  }
  //reject 函数
  function reject(data) {
    //判断状态(状态只能修改一次)
    if (self.PromiseState !== "pending") return;
    //1. 修改对象的状态 (promiseState)
    self.PromiseState = "rejected"; //
    //2. 设置对象结果值 (promiseResult)
    self.PromiseResult = data;
    //执行失败的回调
    self.callbacks.forEach((item) => {
      item.onRejected(data);
    });
  }
  try {
    //同步调用『执行器函数』
    executor(resolve, reject);
  } catch (e) {
    //修改 promise 对象状态为『失败』
    reject(e);
  }
}

//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
  //调用回调函数  PromiseState
  //then方法执行回调
  if (this.PromiseState === "fulfilled") {
    onResolved(this.PromiseResult);
  }
  if (this.PromiseState === "rejected") {
    onRejected(this.PromiseResult);
  }
  //判断 pending 状态
  //异步任务then方法执行回调
  if (this.PromiseState === "pending") {
    //保存回调函数
    this.callbacks.push({
      onResolved: onResolved,
      onRejected: onRejected,
    });
  }
};
```

### 4、同步修改状态 then 返回结果，异步修改状态 then 返回结果

**同步**

```js
Promise.prototype.then = function (onResolved, onRejected) {
  return new Promise((resolve, reject) => {
    //调用回调函数  PromiseState
    if (this.PromiseState === "fulfilled") {
      try {
        //获取回调函数的执行结果
        let result = onResolved(this.PromiseResult);
        //判断
        if (result instanceof Promise) {
          //如果是 Promise 类型的对象
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          //结果的对象状态为『成功』
          resolve(result);
        }
      } catch (e) {
        reject(e);
      }
    }
    if (this.PromiseState === "rejected") {
      onRejected(this.PromiseResult);
    }
    //判断 pending 状态
    if (this.PromiseState === "pending") {
      //保存回调函数
      this.callbacks.push({
        onResolved: onResolved,
        onRejected: onRejected,
      });
    }
  });
};
```

**异步**

```js
//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
  const self = this;
  return new Promise((resolve, reject) => {
    if (this.PromiseState === "fulfilled") {
      //...
    }
    if (this.PromiseState === "rejected") {
      //...
    }
    //判断 pending 状态
    if (this.PromiseState === "pending") {
      //保存回调函数
      this.callbacks.push({
        onResolved: function () {
          try {
            //执行成功回调函数
            let result = onResolved(self.PromiseResult);
            //判断
            if (result instanceof Promise) {
              result.then(
                (v) => {
                  resolve(v);
                },
                (r) => {
                  reject(r);
                }
              );
            } else {
              resolve(result);
            }
          } catch (e) {
            reject(e);
          }
        },
        onRejected: function () {
          try {
            //执行成功回调函数
            let result = onRejected(self.PromiseResult);
            //判断
            if (result instanceof Promise) {
              result.then(
                (v) => {
                  resolve(v);
                },
                (r) => {
                  reject(r);
                }
              );
            } else {
              resolve(result);
            }
          } catch (e) {
            reject(e);
          }
        },
      });
    }
  });
};
```

### 5、then 方法优化,catch 方法与异常穿透，值传递

```js
//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
  const self = this;
  //判断回调函数参数
  //异常穿透
  if (typeof onRejected !== "function") {
    onRejected = (reason) => {
      throw reason;
    };
  }
  //值传递
  if (typeof onResolved !== "function") {
    onResolved = (value) => value;
    //value => { return value};
  }
  return new Promise((resolve, reject) => {
    //封装函数
    function callback(type) {
      try {
        //获取回调函数的执行结果
        let result = type(self.PromiseResult);
        //判断
        if (result instanceof Promise) {
          //如果是 Promise 类型的对象
          result.then(
            (v) => {
              resolve(v);
            },
            (r) => {
              reject(r);
            }
          );
        } else {
          //结果的对象状态为『成功』
          resolve(result);
        }
      } catch (e) {
        reject(e);
      }
    }
    //调用回调函数  PromiseState
    if (this.PromiseState === "fulfilled") {
      callback(onResolved);
    }
    if (this.PromiseState === "rejected") {
      callback(onRejected);
    }
    //判断 pending 状态
    if (this.PromiseState === "pending") {
      //保存回调函数
      this.callbacks.push({
        onResolved: function () {
          callback(onResolved);
        },
        onRejected: function () {
          callback(onRejected);
        },
      });
    }
  });
};

//添加 catch 方法
Promise.prototype.catch = function (onRejected) {
  return this.then(undefined, onRejected);
};
```

### 6、Promise.resolve,Promise,reject,Promise,all,Promise.race 实现

```js
//添加 resolve 方法
Promise.resolve = function (value) {
  //返回promise对象
  return new Promise((resolve, reject) => {
    if (value instanceof Promise) {
      value.then(
        (v) => {
          resolve(v);
        },
        (r) => {
          reject(r);
        }
      );
    } else {
      //状态设置为成功
      resolve(value);
    }
  });
};

//添加 reject 方法
Promise.reject = function (reason) {
  return new Promise((resolve, reject) => {
    reject(reason);
  });
};

//添加 all 方法
Promise.all = function (promises) {
  //返回结果为promise对象
  return new Promise((resolve, reject) => {
    //声明变量
    let count = 0;
    let arr = [];
    //遍历
    for (let i = 0; i < promises.length; i++) {
      //
      promises[i].then(
        (v) => {
          //得知对象的状态是成功
          //每个promise对象 都成功
          count++;
          //将当前promise对象成功的结果 存入到数组中
          arr[i] = v;
          //判断
          if (count === promises.length) {
            //修改状态
            resolve(arr);
          }
        },
        (r) => {
          reject(r);
        }
      );
    }
  });
};

//添加 race 方法
Promise.race = function (promises) {
  return new Promise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(
        (v) => {
          //修改返回对象的状态为 『成功』
          resolve(v);
        },
        (r) => {
          //修改返回对象的状态为 『失败』
          reject(r);
        }
      );
    }
  });
};
```

### 7、then 回调函数异步执行

```js
function Promise(executor) {
  //...
  //resolve 函数
  function resolve(data) {
    //...
    //调用成功的回调函数
    setTimeout(() => {
      self.callbacks.forEach((item) => {
        item.onResolved(data);
      });
    });
  }
  //reject 函数
  function reject(data) {
    //...
    //执行失败的回调
    setTimeout(() => {
      self.callbacks.forEach((item) => {
        item.onRejected(data);
      });
    });
  }
}
//添加 then 方法
Promise.prototype.then = function (onResolved, onRejected) {
  //...
  return new Promise((resolve, reject) => {
    if (this.PromiseState === "fulfilled") {
      setTimeout(() => {
        callback(onResolved);
      });
    }
    if (this.PromiseState === "rejected") {
      setTimeout(() => {
        callback(onRejected);
      });
    }
    //判断 pending 状态
    if (this.PromiseState === "pending") {
      //保存回调函数
      this.callbacks.push({
        onResolved: function () {
          callback(onResolved);
        },
        onRejected: function () {
          callback(onRejected);
        },
      });
    }
  });
};
```
