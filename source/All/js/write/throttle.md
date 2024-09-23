---
title: 节流函数
date: 2021-07-18 21:04:24
toc: true
---

>节流函数比 防抖 要宽松些，这时我们不想用户一味的输入，而是给用户一些搜索提示，所以在当中限制每过500ms就查询一次此时的String，这就是节流。
原理：节流函数不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数。

```js
function throttle(func, delay) {
  let timer;
  return function() {
    if (!timer) {
      timer = setTimeout(function() {
        func();
        timer = null;
      }, delay)
    }
  }
}
```