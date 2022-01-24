---
title: Axios基础结构模拟实现
date: 2021-11-09 10:47:24
toc: true
---

**源码片段**
**入口文件`node_modules/axios/index.js`**

```js
module.exports = require("./lib/axios");
```

```js
//lib/axios.js
//axios的入口文件
//引入工具，绑定函数，Axios构造函数，默认配置等

//* 重要 * createInstance函数
function createInstance(defaultConfig) {}

var axios = createInstance(defaults);
//.... 为axios添加各种属性,例如create方法，all, Axios，Cancel...

module.exports.default = axios;
```

**下面来分析一下 createInstance 函数**
接收一个参数，默认配置,从外部引入的配置

```js
/**
 * Create an instance of Axios
 * 创建一个 Axios 的实例对象
 * @param {Object} defaultConfig The default config for the instance
 * @return {Axios} A new instance of Axios
 */
function createInstance(defaultConfig) {
  //创建一个实例对象 context可以调用get post put delete request
  var context = new Axios(defaultConfig); //context不能当作函数来使用
  //将 request 方法的this指向context并返回新函数， instance可以用作函数使用，且返回一个promise对象
  var instance = bind(Axios.prototype.request, context); //instance 与 Axios.prototype.request代码一致
  //instance({ method: 'get' }); instance.get() ...
  //Copy axios.prototype to instance
  //将Axios.prototype和实例对象的方法都添加到instance上
  utils.extend(instance, Axios.prototype, context);
  //将实例对象的方法和属性扩展到instance函数身上
  utils.extend(instance, context);

  return instance;
}
```

根据上面的描述，我们可以知道我们使用 axios，是通过 createInstance 函数生成的一个 instance 实例对象，既可以当成函数直接使用，也可以当成对象使用，调用上面的属性方法

**所以，我们可以构造出基础的结构**

```js
//Axios构造函数
function Axios(config) {
  //初始化
  this.defaults = config; //为了创建defaults默认值
  //拦截器
  this.interceptors = {
    request: {},
    response: {},
  };
}
//Axios原型上有一系列方法，我们这里只实现request（最重要）,get和post(最常用)的
Axios.prototype.request = function (config) {
  console.log("发送 AJAX 请求 请求的类型为 " + config.method);
};
Axios.prototype.get = function (config) {
  return this.request({ method: "GET" });
};
Axios.prototype.post = function (config) {
  return this.request({ method: "POST" });
};

//声明函数createInstance
//接收的config是默认参数
function createInstance(config) {
  //实例化一个对象
  let context = new Axios(config); //context.get() context.post()
  //创建请求函数
  let instance = Axios.prototype.request.bind(context); //instance是一个函数，并且可以 instance({}) 但是不能instance.get X
  //将Axios.prototype对象中的方法添加到instance函数对象中
  Object.keys(Axios.prototype).forEach((key) => {
    instance[key] = Axios.prototype[key].bind(context); //this.default this.interceptors
  });
  //为instance函数对象添加属性default与interceptors
  Object.keys(context).forEach((key) => {
    instance[key] = context[key];
  });

  return instance;
}

//这里暂不模拟默认参数，所以createInstance不接受参数
let axios = createInstance();

//以下是测试代码
//发送请求
axios({ method: "POST" });//发送 AJAX 请求 请求的类型为POST
axios.get({});//发送 AJAX 请求 请求的类型为 GET
axios.post({});//发送 AJAX 请求 请求的类型为 POST
```
