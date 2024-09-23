---
title: 什么是数据响应式
date: 2022-01-17 09:53:24
toc: true
---

![reactive](/assets/vueImg/vueSource/reactive1.png "reactive")

**从 MVVM 模式来看**
`<p>我{{age}}岁了</p>`
数据变化 => `this.age++;`
数据变化，视图会自动变化
![reactive](/assets/vueImg/vueSource/reactive2.png "reactive")

**侵入式和非侵入式**
![reactive](/assets/vueImg/vueSource/reactive3.png "reactive")

**Object.defineProperty()**

> 数据劫持/数据代理

`Object.defineProperty()`方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

```js
var obj = {};
Object.defineProperty(obj, "a", {
  value: 3,
});
Object.defineProperty(obj, "b", {
  value: 5,
});
console.log(obj);
console.log(obj.a, obj.b);
```

`Object.defineProperty()`方法可以设置一些额外隐藏的属性。

```js
Object.defineProperty(obj, "a", {
  value: 3,
  // 是否可写
  writable: false,
});

Object.defineProperty(obj, "b", {
  value: 5,
  // 是否可以被枚举
  enumerable: false,
});
```

![reactive](/assets/vueImg/vueSource/reactive4.png "reactive")

```js
Object.defineProperty(obj, "a", {
  // getter
  get() {
    console.log("你试图访问obj的a属性");
  },
  // setter
  set() {
    console.log("你试图改变obj的a属性");
  },
});
console.log(obj.a);
obj.a = 10;
```
