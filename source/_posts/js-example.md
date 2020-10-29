---
title: js例子
date: 2020-10-28 10:15:22
tags: js
---
## js的几个问题

### 问题一：浏览器的console里会打印出什么?
```js
var a = 10;
function foo() {
    console.log(a); // ??
    var a = 20;
}
foo();
```
**答案：undefined**
**解释：使用var关键字声明的变量被提升并在内存中为其赋值为undefined.但是初始化恰好发生在你在代码中写入它们的地方。另外，var声明的变量是函数作用域，而let和const是块作用域。所以，这就是这个所从的样子：**
```js
var a = 10; // 全局作用域
function foo() {
// 使用var声明的会被提升到函数作用域内顶部.
// 就像: var a;

console.log(a); // 打印 undefined

// 实际初始化值20只发生在这里
   var a = 20; // 本地 scope
}
```
### 问题二：
```js
var a = 10;
function foo() {
    console.log(a); // ??
    let a = 20;
}
foo();
```
**解释**
```js

```
### 问题三：
```js
var array = [];
for(var i = 0; i <3; i++) {
 array.push(() => i);
}
var newArray = array.map(el => el());
console.log(newArray); // ??
```
**解释**
```js

```
### 问题四：
```js
function foo() {
  setTimeout(foo, 0); // will there by any stack overflow error?
};
```
**解释**
```js

```
### 问题五：
```js
function foo() {
  return Promise.resolve().then(foo);
};
```
**解释**
```js

```
### 问题六：
```js
var obj = { x: 1, y: 2, z: 3 };
[...obj]; // TypeError
```
**解释**
```js

```
### 问题七：
```js
var obj = { a: 1, b: 2 };
Object.setPrototypeOf(obj, {c: 3});
Object.defineProperty(obj, 'd', { value: 4, enumerable: false });

// what properties will be printed when we run the for-in loop?
for(let prop in obj) {
    console.log(prop);
}
```
**解释**
```js

```
### 问题八：
```js
var x = 10;
var foo = {
  x: 90,
  getX: function() {
    return this.x;
  }
};
foo.getX(); // prints 90
var xGetter = foo.getX;
xGetter(); // prints ??
```
**解释**
```js

```



