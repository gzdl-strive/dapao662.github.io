---
title: 防抖函数
date: 2020-12-18 16:58:16
tag: js
---

### 防抖函数
>原理：将若干个函数调用合成为一次，并在给定时间过去之后仅被调用一次
```js
function debounce(fn, delay) {
    //维护一个timer,用来记录当前执行函数状态
    let timer = null;
    return function() {
        //通过this和arguments获取函数作用域和遍历
        let context = this;
        let args = arguments;
        //清理正在执行的函数，并重新执行
        clearTimeout(timer);
        timer = setTimeout(function() {
            fn.apply(fn, args);
        }, delay);
    }
}
let flag = 0;//记录当前函数调用次数
//当用户滚动时被调用的函数
function foo() {
    flag++;
    console.log(`flag 调用次数${flag}`);
}
//在debounce中包装我们的函数，返回内部函数
(function () {
    window.addEventListener('scroll', debounce(foo, 2000));
})();
//debounce函数封装后，返回内部函数
//每一次事件被触发，都会清除当前的timer然后重新设置超时并调用。这会导致每一次高频事件都会取消前一次的超时调用，导致事件处理程序不能被触发
//只有当高频事件停止，最后一次事件触发的超时调用才能在delay时间后执行
```