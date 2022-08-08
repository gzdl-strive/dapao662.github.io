---
title: 懒加载
date: 2022-08-08 09:55:24
toc: true
---

## 懒加载

### 方法一：通过元素的offsetTop和clientHeight以及scrollTop进行比较判断图片是否出现在视口
```js
function lazyload() {
  const imgs = document.querySelectAll('img[data-src]');
  const len = imgs.length;
  // 视口高度
  const viewHeight = document.documentElement.clienHeight;
  // 滚动条高度
  const scrollHeight = document.documentElement.scrollTop || document.body.scrollTop;
  for (let i = 0; i < len; i++) {
    const offsetHeight = imgs[i].offsetTop;
    if (offsetHeight < viewHeight + scrollHeight) {
      const src = imgs[i].dataset.src;
      imgs[i].src = src;
    }
  }
}

// 可以使用节流来优化
window.addEventListener('scroll', lazyload);
```

### 方法二、Element.getBoundingClientRect()方法返回元素的大小及其相对于视口的位置
```js
function lazyload() {
  const imgs = document.querySelectorAll('img[data-src]');
  const len = imgs.length;
  for (let i = 0; i < len; i++) {
    if (img[i].getBoundingClientRect().top < document.documentElement.clientHeight) {
      imgs[i].src = imgs[i].dataset.src;
    }
  }
}
(function() {
  window.addEventListener('scroll', lazyload);
})();
```

## 滚动加载
```js
// 滚动加载
window.addEventListener('scroll', function() {
  // 视口高度
  const clientHeight = document.documentElement.clientHeight;
  // 滚动条高度
  const scrollTop = document.documentElement.scrollTop;
  const scrollHeight = document.documentElement.scrollHeight;
  if (clientHeight + scrollTop >= scrollHeight) {
    // 检测到滚动至页面底部，进行后续操作
  }
});
```