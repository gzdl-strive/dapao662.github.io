---
title: axios-默认配置
date: 2021-11-09 10:10:24
toc: true
---

**html**

```html
<div class="container">
  <button class="btn btn-primary">发送GET请求</button>
</div>
```

**js**

```js
//获取按钮
const btns = document.querySelectorAll("button");
//默认配置
axios.defaults.method = "GET"; //设置默认的请求类型为 GET
axios.defaults.baseURL = "http://localhost:3000"; //设置基础 URL
axios.defaults.params = { id: 100 };
axios.defaults.timeout = 3000;

btns[0].onclick = function () {
  axios({
    url: "/posts",
  }).then((response) => {
    console.log(response);
  });
};
```
