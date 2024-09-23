---
title: defineReactive函数
date: 2022-01-17 10:06:24
toc: true
---

**getter/setter需要变量周转才能工作**
```js
let temp;
Object.defineProperty(obj, 'a', {
  //getter
  get() {
    console.log('你试图访问obj的a属性');
    return temp;
  },
  //setter
  set(newValue) {
    console.log('你试图修改obj的a属性', newValue);
    temp = newValue;
  }
})
```
>通过使用一个变量来周转

**不需要设置临时变量，使用闭包**
```js
function defineReactive(data, key, val) {
  Object.defineProperty(data, key, {
    //可枚举
    enumerable: true,
    //可配置
    configurable: true,
    //getter
    get() {
      console.log('你正在访问obj的' + key + '属性');
      return val;
    },
    //setter
    set(newValue) {
      console.log('你试图改变obj的' + key + '属性', newValue);
      if (val === newValue) return;
      val = newValue;
    },
  })
}
```