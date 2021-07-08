---
title: 防抖函数
date: 2021-07-07 21:32:24
toc: true
---

## 防抖函数
当持续触发事件的时候，一定时间内没有再触发事件，事件处理函数才执行一次，如果设定的时间在到来之前又一次触发了事件，就重新开始延时。

触发事件->一段时间内->没有触发->事件执行->肯定使用定时器

在设定的时间内，又一次触发，重新开始延时，代表的就是重新开始定时器

那么意味着上一次还没有结束的定时器要清除掉 重新开始

### 初级版本
```js
/**
 * {delay} 延时时间
 */
function debounce(delay) {
  let timer;
  clearTimeout(timer);
  timer = setTimeout(function() {
    // todo ...
  }, delay);
}
```
改防抖函数在运行时，每次都会清除`timer`,但是由于timer是局部变量，所以每次清除的都是`undefined`，所以改防抖函数没有达到清除的效果。

### 改进版
>思路：因为上面初级版本的`timer`变量是局部变量，所以每次清除的都是`undefined`，所以我们要保存这个timer变量，那么timer变量需要一直保存在内存中--》想把一个变量保存在内存当中，需要用到**闭包**
所以我们可以得出以下改进版防抖函数
```js
function debounce(delay) {
  let timer;
  return function() {
    clearTimeout(timer);
    timer = setTimeout(function() {
      //todo...
    }, delay);
  }
}
```






