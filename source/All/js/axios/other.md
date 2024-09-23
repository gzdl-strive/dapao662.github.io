---
title: axios的其他使用
date: 2021-11-09 10:03:24
toc: true
---

**html**

```html
<div class="container">
  <h2 class="page-header">其他使用</h2>
  <button class="btn btn-primary">发送GET请求</button>
  <button class="btn btn-warning">发送POST请求</button>
</div>
```

**js**

```js
//获取按钮
const btns = document.querySelectorAll("button");

//发送GET请求
btns[0].onclick = function () {
  // axios()
  axios
    .request({
      method: "GET",
      url: "http://localhost:3000/comments",
    })
    .then((response) => {
      console.log(response);
    });
};

//发送 POST 请求
btns[1].onclick = function () {
  // axios()
  axios
    .post("http://localhost:3000/comments", {
      body: "喜大普奔666",
      postId: 2,
    })
    .then((response) => {
      console.log(response);
    });
};
```
