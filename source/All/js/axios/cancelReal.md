---
title: Axios取消请求模拟实现
date: 2021-11-09 16:06:24
toc: true
---

**中断请求的使用**

```js
let cancel = null;
//发送请求
btns[0].onclick = function () {
  //检测上一次的请求是否已经完成
  if (cancel !== null) {
    //取消上一次的请求
    cancel();
  }

  //创建 cancelToken 的值
  let cancelToken = new axios.CancelToken(function (c) {
    cancel = c;
  });

  axios({
    method: "GET",
    url: "http://localhost:3000/posts",
    //1. 添加配置对象的属性
    cancelToken: cancelToken,
  }).then((response) => {
    console.log(response);
    //将 cancel 的值初始化
    cancel = null;
  });
};

//绑定第二个事件取消请求
btns[1].onclick = function () {
  cancel();
};
```

由代码的使用可以得知，通过在配置项中添加 cancelToken 配置项，该配置项调用的是
**源码片段**
`node_modules/axios/lib/cancel/CancelToken.js`

```js
/**
 * A `CancelToken` is an object that can be used to request cancellation of an operation.
 * 构造函数   new axios.CancelToken
 * @class
 * @param {Function} executor The executor function. 执行器函数必须是一个函数
 */
function CancelToken(executor) {
  //执行器函数必须是一个函数
  if (typeof executor !== "function") {
    throw new TypeError("executor must be a function.");
  }

  //声明一个变量
  var resolvePromise; //  resolvePromise()
  //实例对象身上添加 promise 属性
  this.promise = new Promise(function promiseExecutor(resolve) {
    //将修改 promise 对象成功状态的函数暴露出去
    resolvePromise = resolve;
  });
  // token 指向当前的实例对象
  var token = this;
  //将修改 promise 状态的函数暴露出去, 通过 cancel = c 可以将函数赋值给 cancel
  executor(function cancel(message) {
    if (token.reason) {
      // Cancellation has already been requested
      return;
    }

    token.reason = new Cancel(message);
    resolvePromise(token.reason);
  });
}
```

`node_modules/axios/lib/adapters/xhr.js`

```js
module.exports = function xhrAdapter(config) {
  //...
  //如果配置了 cancelToken 则调用 then 方法设置成功的回调
  if (config.cancelToken) {
    // Handle cancellation
    config.cancelToken.promise.then(function onCanceled(cancel) {
      if (!request) {
        return;
      }
      //取消请求
      request.abort();
      reject(cancel);
      // Clean up request
      request = null;
    });
  }
  //...
};
```

**通过上面的片段可以得知，在配置项中假如 cancelToken 配置项（该配置项是通过 CancelToken 构造函数创建出来的实例，那么该实例上存在 promise 属性，用于表示 promise 的状态），那么在适配器 adapter 中会判断是否存在 cancelToken 配置项，如果存在，则会调用 cancelToken 上的 promise 属性，通过 promise 的 then 方法，当我们改变了该 promise 的状态，就中断请求**

**模拟实现如下**

```js
//CancelToken构造函数
//接收一个执行器函数参数
function CancelToken(executor) {
  //声明一个遍历
  let resolvePromise;
  //为实例对象添加属性
  this.promise = new Promise((resolve) => {
    //将resolve赋值给 resolvePromise
    //将resolve暴露出去，外面只需要调用resolvePromise就可以改变promise的状态
    resolvePromise = resolve;
  });
  //调用执行器函数
  executor(function () {
    //指向resolvePromise函数
    resolvePromise();
  });
}
//这里的executor函数，就是我们传入的函数
/*
 executor   ===   function (c) {
                    cancel = c;
                  }
  c === function () {resolvePromise()}
  只有c函数调用那么就会改变promise的状态，而c又被赋值给了cancel全局变量，所以我们只需要调用cancel函数，就能中断请求
*/
```

我们还需要修改适配器中的代码

```js
function xhrAdapter(config) {
  return new Promise((resolve, reject) => {
    //发送请求
    //...

    //关于取消请求的处理
    if (config.cancelToken) {
      //只有promise状态改变为fulfilled，那么就会中断请求
      config.cancelToken.promise.then((value) => {
        xhr.abort();
        //将整体结果设置为失败
        reject(new Error("请求已经被取消"));
      });
    }
  });
}
```

**测试代码**

```js
//获取按钮 以上为模拟实现的代码
const btns = document.querySelectorAll("button");
//声明全局变量
let cancel = null;
//发送请求
btns[0].onclick = function () {
  //检测上一次的请求是否已经完成
  if (cancel !== null) {
    //取消上一次的请求
    cancel();
  }

  //创建 cancelToken 的值
  let cancelToken = new CancelToken(function (c) {
    cancel = c;
  });

  axios({
    method: "GET",
    url: "http://localhost:3000/posts",
    //1. 添加配置对象的属性
    cancelToken: cancelToken,
  }).then((response) => {
    console.log(response);
    //将 cancel 的值初始化
    cancel = null;
  });
};

//绑定第二个事件取消请求
btns[1].onclick = function () {
  cancel();
};
```
