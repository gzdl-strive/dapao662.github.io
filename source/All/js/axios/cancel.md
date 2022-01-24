---
title: axios-取消请求
date: 2021-11-09 10:33:24
toc: true
---

**html**

```js
<div class="container">
  <h2 class="page-header">axios取消请求</h2>
  <button class="btn btn-primary"> 发送请求 </button>
  <button class="btn btn-warning"> 取消请求 </button>
</div>
```

**js**

```js
//获取按钮
const btns = document.querySelectorAll('button');
//声明全局变量
let cancel = null;
//发送请求
btns[0].onclick = function() {
  //检测上一次的请求是否已经完成
  if (cancel !== null) {
    //取消上一次的请求
    cancel();
  }
  axios({
    method: 'GET',
    url: 'http://localhost:3000/posts',
    //添加配置对象的属性
    cancelToken: new axios.CancelToken(function(c) {
      //将c赋值给cancel
      cancel = c;
    })
  }).then(response => {
    console.log(repsonse);
    //初始化cancel值
    cancel = null;
  })
}

//绑定第二个事件-取消请求
btns[1].onclick = function () {
  cancel();
}
```
