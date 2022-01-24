---
title: Axios拦截器模拟实现
date: 2021-11-09 15:42:24
toc: true
---

如果存在多个请求拦截器，那么输出顺序是逆序的
如果存在多个响应拦截器，那么输出顺序是正序的

```js
/*
  请求拦截器 成功 - 2号
  请求拦截器 成功 - 1号
  响应拦截器 成功 1号
  响应拦截器 成功 2号
*/
```

**拦截器使用**

```js
// 设置请求拦截器  config 配置对象
axios.interceptors.request.use(
  function one(config) {
    console.log("请求拦截器 成功 - 1号");
    return config;
  },
  function one(error) {
    console.log("请求拦截器 失败 - 1号");
    return Promise.reject(error);
  }
);
```

axios 对象身上有 interceptors 属性，该属性是一个对象，里面的存在 request 属性，该属性能调用器原型上的 use 方法

**源码片段**
`node_modules/axios/lib/core/Axios.js`

```js
/**
 * Create a new instance of Axios
 * 创建 Axios 构造函数
 * @param {Object} instanceConfig The default config for the instance
 */
function Axios(instanceConfig) {
  //实例对象上的 defaults 属性为配置对象
  this.defaults = instanceConfig;
  //实例对象上有 interceptors 属性用来设置请求和响应拦截器
  this.interceptors = {
    request: new InterceptorManager(),
    response: new InterceptorManager(),
  };
}
```

**InterceptorManager 函数**
`node_modules/axios/lib/core/InterceptorManager.js`

```js
//声明构造函数
//axios.interceptors.request.use
//axios.interceptors.response.use
function InterceptorManager() {
  //创建一个属性
  this.handlers = [];
}
/**
 * Add a new interceptor to the stack
 * 添加拦截器到栈中, 以待后续执行, 返回拦截器的编号(编号为当前拦截器综合数减一)
 * @param {Function} fulfilled The function to handle `then` for a `Promise`
 * @param {Function} rejected The function to handle `reject` for a `Promise`
 *
 * @return {Number} An ID used to remove interceptor later
 */
InterceptorManager.prototype.use = function use(fulfilled, rejected) {
  this.handlers.push({
    fulfilled: fulfilled,
    rejected: rejected,
  });
  return this.handlers.length - 1;
};
```

**由上面代码可以分析得，Axios 上存在 interceptors 属性，该属性存在 request 和 response 属性，这两个属性是通过 InterceptorManager 拦截器管理器构造出来的实例，那么这两个属性身上有 handlers 数组，且其原型对象上存在 use 方法，调用 use 方法时，会传入成功的回调和失败的回调，在 use 里面会把这两个回调存到 handlers 数组中**

**模拟实现如下**

```js
//构造函数
function Axios(config) {
  this.defaults = config;
  this.interceptors = {
    request: new InterceptorManager(),
    response: new InterceptorManager(),
  };
}
//拦截器管理器构造函数
function InterceptorManager() {
  this.handlers = [];
}
InterceptorManager.prototype.use = function (fulfilled, rejected) {
  this.handlers.push({
    fulfilled,
    rejected,
  });
};
```

**我们还需要修改之前 reqeust 函数中的代码，把拦截器功能加上**

```js
Axios.prototype.reqeust = function (config) {
  //创建一个promise对象
  let promise = Promise.resolve(config);
  //创建一个数组
  const chains = [dispatchRequest, undefined];
  //处理拦截器
  //请求拦截器 将请求拦截器的回调 压入到 chains 的前面  request.handles = []
  this.interceptors.request.handlers.forEach((item) => {
    chains.unshift(item.fulfilled, item.rejected);
  });
  //响应拦截器
  this.interceptors.response.handlers.forEach((item) => {
    chains.push(item.fulfilled, item.rejected);
  });

  //遍历
  while (chains.length) {
    promise = promise.then(chains.shift(), chains.shift());
  }

  return promise;
};
```

**测试代码**

```js
axios.interceptors.request.use(
  function one(config) {
    console.log("请求拦截器 成功 - 1号");
    return config;
  },
  function one(error) {
    console.log("请求拦截器 失败 - 1号");
    return Promise.reject(error);
  }
);

axios.interceptors.request.use(
  function two(config) {
    console.log("请求拦截器 成功 - 2号");
    return config;
  },
  function two(error) {
    console.log("请求拦截器 失败 - 2号");
    return Promise.reject(error);
  }
);

// 设置响应拦截器
axios.interceptors.response.use(
  function (response) {
    console.log("响应拦截器 成功 1号");
    return response;
  },
  function (error) {
    console.log("响应拦截器 失败 1号");
    return Promise.reject(error);
  }
);

axios.interceptors.response.use(
  function (response) {
    console.log("响应拦截器 成功 2号");
    return response;
  },
  function (error) {
    console.log("响应拦截器 失败 2号");
    return Promise.reject(error);
  }
);

//发送请求
axios({
  method: "GET",
  url: "http://localhost:3000/posts",
}).then((response) => {
  console.log(response);
});

/*
请求拦截器 成功 - 2号
请求拦截器 成功 - 1号
响应拦截器 成功 1号
响应拦截器 成功 2号
{status: 200, statusText: 'OK'}
*/
```
