---
title: 什么是变量提升
date: 2021-03-10 15:21:24
tag: js
---

```js
console.log(a);//undefined
var a = 10;
```
>定义：变量提升是当栈内存作用域形成时，js代码执行前，浏览器会将带有var, function 关键字的变量提前声明declare(值默认就是undefined),定义defined(就是赋值操作),这种预先处理的机制就叫做变量提升机制也叫预定义

>在变量提升阶段：带var的只声明还没有被定义，带function的已经声明和定义。所以在代码执行前，有带var的就提前声明，比如这里的a就赋值成undefined,在代码执行过程中遇到创建函数的代码浏览器会直接跳过

**讲解示例**
```js
var a = 12;
var b = a;
b = 1;
function sum(x, y) {
    var total = x + y;
    return total;
}
sum(1, 2);
```
![1](/assets/jsImg/variablePromotion/1.png "变量提升")
**变量提升只发生在当前作用域。比如：在页面开始加载时，只有全局作用域发生变量提升，这时候的函数中存储的都是代码字符串。**