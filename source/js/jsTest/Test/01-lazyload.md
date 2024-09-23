---
title: 懒加载
date: 2020-12-08 22:27:16
tag: js
---

> 懒加载，顾名思义，在当前网页，滑动页面到能看到图片的时候再加载图片

**故问题拆分成两个：**

1. 如何判断图片出现在了当前视口 （即如何判断我们能够看到图片）
2. 如何控制图片的加载

## 方案一

* 如何判断图片出现在了当前视口

`clientTop` ， `offsetTop` ， `clientHeight` 以及 `scrollTop` 各种关于图片的高度作比对这些高度都代表了什么意思？仅仅知道它静态的高度还不够，我们还需要知道动态的

* 如何动态？监听 `window.scroll` 事件
* 如何控制图片的加载

``` html
<img data-src="1.png" />
```

首先设置一个临时属性 `data-src` , 控制加载时使用 `src` 代替 `data-src`

``` js
//<img alt="lalalala" data-src="./zero.png"/>
function lazyload() {
    const imgs = document.querySelectorAll('img[data-src]');
    const len = imgs.length;
    //视口的高度
    const viewHeight = document.documentElement.clientHeight;
    const scrollHeight = document.documentElement.scrollTop || document.body.scrollTop;
    for (let i = 0; i < len; i++) {
        const offsetHeight = imgs[i].offsetTop;
        if (offsetHeight < viewHeight + scrollHeight) {
            imgs[i].src = imgs[i].dataset.src;
        }
    }
}
(function() {
    window.addEventListener('scroll', lazyload);
})();
```

## 方案二

* 如何判断图片出现在了当前视口

**引入一个新的 API， ` Element.getBoundingClientRect() ` 方法返回元素的大小及其相对于视口的位置。**

![1](/assets/jsImg/Test/lazyload.png "1")

那如何判断图片出现在了当前视口呢，根据示例图示意，代码如下:

``` js
//clientHeight 代表当前视口的高度
img.getBoundingClientRect().top < document.documentElement.clientHeight;
```

监听 `window.scroll` 事件也优化一下
加个节流器，提高性能。工作中一般使用 `lodash.throttle` 就可以了，万能的 `lodash` 啊

``` js
_.throttle(func, [wait = 0], [options = {}])
```

``` js
//<img alt="lalalala" data-src="./zero.png"/>
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

## 方案三

* 如何判断图片出现在了当前视口

方案二使用的方法是: `window.scroll` 监听 `Element.getBoundingClientRect() ` 并使用 ` _.throttle` 节流
一系列组合动作太复杂了，于是浏览器出了一个三合一事件: `IntersectionObserver` API，一个能够监听元素是否到了当前视口的事件，一步到位！
事件回调的参数是 `IntersectionObserverEntry` 的集合，代表关于是否在可见视口的一系列值. 其中， `entry.isIntersecting` 代表目标元素可见

``` js
const observer = new IntersectionObserver((changes) => {
    changes.forEach((change) => {
        if (change.isIntersecting) {
            const img = change.target;
            img.src = img.dataset.src;
            observer.unobserve(img);
        }
    })
})
const imgs = document.querySelectorAll('img[data-src]');
const len = imgs.length;
for (let i = 0; i < len; i++) {
    observer.observe(imgs[i])
}
```

## 方案四

浏览器觉得懒加载这事可以交给自己做，你们开发者加个属性就好了。实在是...！

``` html
<img src="shanyue.jpg" loading="lazy">
```

不过目前浏览器兼容性不太好
