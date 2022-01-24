---
title: Axios请求发送模拟实现
date: 2021-11-09 15:09:24
toc: true
---

我们已经把基础结构搭建完成，但是还不能发送请求，我们从源码中可以得知，不管是 get 还是 post...方法，最后都是通过调用 request 方法来发送的。

**源码片段-`node_modules/axios/lib/core/Axios.js`**
该文件定义了 Axios 构造函数以及原型方法

```js
function Axios(config) {
  //...
}
/**
 * Dispatch a request
 * 发送请求的方法.  原型上配置, 则实例对象就可以调用 request 方法发送请求
 * @param {Object} config The config specific for this request (merged with this.defaults)
 */
Axios.prototype.request = function (config) {
  /**
   * axios('http://www.baidu.com', {header:{}})
   */
  if (typeof config === "string") {
    config = arguments[1] || {};
    config.url = arguments[0];
  } else {
    config = config || {};
  }
  //将默认配置与用户调用时传入的配置进行合并
  config = mergeConfig(this.defaults, config);

  // Set config.method
  // 设定请求方法
  if (config.method) {
    config.method = config.method.toLowerCase();
  } else if (this.defaults.method) {
    config.method = this.defaults.method.toLowerCase();
  } else {
    config.method = "get";
  }

  //Hook up interceptors middleware
  //创建蓝机器中间件 第一个参数用来发送请求，第二个为undefined 用来补位
  var chain = [dispatchRequest, undefined];
  // 创建一个成功的 promise 且成功的值为合并后的请求配置
  var promise = Promise.resolve(config);
  // 遍历实例对象的请求拦截器,
  this.interceptors.request.forEach(function unshiftRequestInterceptors(
    interceptor
  ) {
    //将请求拦截器压入数组的最前面
    chain.unshift(interceptor.fulfilled, interceptor.rejected);
  });

  this.interceptors.response.forEach(function pushResponseInterceptors(
    interceptor
  ) {
    //将相应拦截器压入数组的最尾部
    chain.push(interceptor.fulfilled, interceptor.rejected);
  });
  //如果链条长度不为 0
  while (chain.length) {
    //依次取出 chain 的回调函数, 并执行
    promise = promise.then(chain.shift(), chain.shift());
  }
  return promise;
};
```

`node_modules/axios/lib/core/dispatchRequest.js`

```js
module.exports = function dispatchRequest(config) {
  //... 对数据进行处理，判断头信息，对请求数据转化...

  //获取适配器对象，http(node) xhr(浏览器环境)
  var adapter = config.adapter || defaults.adapter;

  //发送请求
  return adapter(config).then(
    function onAdapterResolution(response) {
      //...
    },
    function onAdapterRejection(reason) {
      //...
    }
  );
};
```

**通过源码可以得知，request 函数中调用了 dispatchRequest 函数，在 dispatchRequest 中调用适配器发送请求**
所以我们可以模拟出

```js
//request方法
Axios.prototype.request = function (config) {
  //发送请求
  //创建一个promise对象
  let promise = Promise.resolve(config); //调用Promise.resolve,传入的config对象，所以一定是一个成功的状态Promise,它的结果就是config对象
  //声明一个数组
  let chains = [dispatchRequest, undefined];
  //调用then方法指定回调
  let result = promise.then(chains[0], chains[1]);
  //返回promise的结果
  return result;
};
//dispatchReqeust方法
//Promise内部会给then方法的回调传参，参数就是结果(promiseResult), 即config
function dispatchRequest(config) {
  //调用适配器发送请求
  return xhrAdapter(config).then(
    (response) => {
      //响应的结果进行转换处理
      //...
      return response;
    },
    (error) => {
      throw error;
    }
  );
}
//adapter适配器
function xhrAdapter(config) {
  return new Promise((resolve, reject) => {
    //发送AJAX请求
    let xhr = new XMLHttpRequest();
    //初始化
    xhr.open(config.method, config.url);
    //发送
    xhr.send();
    //绑定事件
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        //判断成功的条件
        if (xhr.status >= 200 && xhr.status < 300) {
          //成功的状态
          resolve({
            //配置对象
            config: config,
            //响应体
            data: xhr.response,
            //响应头
            headers: xhr.getAllResponseHeaders(), //字符串  parseHeaders
            // xhr 请求对象
            request: xhr,
            //响应状态码
            status: xhr.status,
            //响应状态字符串
            statusText: xhr.statusText,
          });
        } else {
          //失败的状态
          reject(new Error("请求失败 失败的状态码为" + xhr.status));
        }
      }
    };
  });
}
```

**测试**
```js
axios({
  method: "GET",
  url: "http://localhost:3000/posts",
}).then(response => {
  console.log(response);
})

/*
{
  config: {...},
  data: "...",
  headers: "...",
  request: XMLHttpRequest,
  status: 200,
  statusText: "OK"
}
*/
```
