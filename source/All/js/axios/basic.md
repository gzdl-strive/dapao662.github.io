---
title: axios的基本使用
date: 2021-11-09 09:50:24
toc: true
---

**html**

```html
<div class="container">
  <h2 class="page-header">基本使用</h2>
  <button class="btn btn-primary">发送GET请求</button>
  <button class="btn btn-warning">发送POST请求</button>
  <button class="btn btn-success">发送 PUT 请求</button>
  <button class="btn btn-danger">发送 DELETE 请求</button>
</div>
```

**js**
```js
//获取按钮
const btns = document.querySelectorAll(".btn");

//第一个按钮，发送GET请求
btns[0].onclick = function () {
  //发送AJAX请求
  axios({
    //请求类型
    method: 'GET',
    //URL
    url: 'http://localhost:3000/posts/2'
  }).then(response => {
    console.log(response);
  })
}

//第二个按钮，发送POST请求，添加一篇新文章
btns[1].onclick = function () {
  //发送AJAX请求
  axios({
    //请求类型
    method: 'POST',
    //URL
    url: 'http://localhost:3000/posts',
    //设置请求体
    data: {
      title: '今天天气不错',
      author: '张三'
    }
  }).then(response => {
    console.log(response);
  })
}

//第三个按钮，发送PUT请求 - 更新数据
btns[2].onclick = function () {
  //发送AJAX请求
  axios({
    method: 'PUT',
    url: 'http://localhost:3000/posts/3',
    //设置请全体
    data: {
      title: "今天天气是真的好",
      author: "李四"
    }
  }).then(response => {
    console.log(response);
  })
}

//第四个按钮，发送delete请求 - 删除数据
btns[3].onclick = function () {
  axios({
    method: 'delete',
    url: 'http://localhost:3000/posts/3'
  }).then(response => {
    console.log(response);
  })
}
```
