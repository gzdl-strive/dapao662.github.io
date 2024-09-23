---
title: 节流函数
date: 2020-12-19 22:10:16
tag: js
---

## 节流函数
>另一种解决方案比 防抖 要宽松些，这时我们不想用户一味的输入，而是给用户一些搜索提示，所以在当中限制每过500ms就查询一次此时的String，这就是节流。
>原理：节流函数不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数。代码实现有两种，一种是时间戳，另一种是定时器

### 1、时间戳
```js
function throttle(fn, delay) {
    let preTime = Date.now();
    return function() {
        let that = this;
        let args = arguments;
        let curTime = Date.now();
        if (curTime - preTime > delay) {
            fn.apply(that, args);
            preTime = Date.now();
        }
    }
}
let flag = 0;
function foo() {
    flag++;
    console.log(`函数执行:${flag}次`);
}
(function() {
    window.addEventListener('scroll', throttle(foo, 2000));
})();
```

### 2、定时器方法
>当触发事件的时候，我们设置一个定时器，再触发事件的时候，如果定时器存在，就不执行；直到delay秒后，定时器执行执行函数，清空定时器，这样就可以设置下个定时器。
```js
function throtte(fn, delay) {
    let timer = null;
    return function() {
        let that = this;
        let args = arguments;
        if (!timer) {
            timer = setTimeout(() => {
                fn.apply(that, args);
                timer = null;
            }, delay);
        }
    }
}
//当第一次触发事件时，肯定不会立即执行函数，而是在delay秒后才执行。
//之后连续不断触发事件，也会每delay秒执行一次。
//当最后一次停止触发后，由于定时器的delay延迟，可能还会执行一次函数
```

### 3、综合使用时间戳与定时器，完成一个事件触发时立即执行，触发完毕还能执行一次的节流函数
>需要在每个delay时间中一定会执行一次函数，因此在节流函数内部使用开始时间、当前时间与delay来计算remaining，当remaining <= 0时表示该执行函数了，如果还没到时间的话就设定在remaining时间后再触发。当然在remaining这段时间中如果又一次发生事件，那么会取消当前的计时器，并重新计算一个remaining来判断当前状态。
```js
function throttle(fn, delay) {
    let timer = null;
    let startTime = Date.now();
    return function() {
        let curTime = Date.now();
        let remaining = delay - (curTime - startTime);
        const that = this;
        const args = arguments;
        clearTimeout(timer);
        if (remaining <= 0) {
            fn.apply(that, args);
            startTime = Date.now();
        } else {
            timer = setTimeout(fn, remaining);
        }
    }
}
```


作者：燕自浩
链接：https://www.jianshu.com/p/7020c9133247
来源：简书