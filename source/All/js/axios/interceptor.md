---
title: axios-拦截器
date: 2021-11-09 10:27:24
toc: true
---

```js
//设置请求拦截器 config配置对象
axios.interceptors.request.use(
  function (config) {
    console.log("请求拦截器 成功 - 1号");
    //修改config中的参数
    config.params = { a: 100 };

    return config;
  },
  function (error) {
    console.log("请求拦截器 失败 - 1号");
    return Promise.reject(error);
  }
);

axios.interceptors.request.use(
  function (config) {
    console.log("请求拦截器 成功 - 2号");
    //修改 config 中的参数
    config.timeout = 2000;
    return config;
  },
  function (error) {
    console.log("请求拦截器 失败 - 2号");
    return Promise.reject(error);
  }
);

// 设置响应拦截器
axios.interceptors.response.use(
  function (response) {
    console.log("响应拦截器 成功 1号");
    return response.data;
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
  console.log("自定义回调处理成功的结果");
  console.log(response);
});
```
