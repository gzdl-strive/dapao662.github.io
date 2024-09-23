---
title: ECMAScript7 新特性
date: 2021-11-16 10:12:24
toc: true
---

## Array.prototype.includes

Includes 方法用来检测数组中是否包含某个元素，返回布尔类型值

```js
const mingzhu = ["西游记", "红楼梦", "三国演义", "水浒传"];

//判断
console.log(mingzhu.includes("西游记"));
console.log(mingzhu.includes("金瓶梅"));
```

## 指数操作符

在 ES7 中引入指数运算符「\*\*」，用来实现幂运算，功能与 Math.pow 结果相同

```js
console.log(2 ** 10); //
console.log(Math.pow(2, 10));
```
