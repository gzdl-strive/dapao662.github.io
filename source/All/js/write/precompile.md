---
title: 预编译练习
date: 2021-07-05 20:33:24
toc: true
---

题目如下：
```js
function fn(a, c) {
  console.log(a);
  var a = 123;
  console.log(a);
  console.log(c);
  function a() {};
  console.log(d);
  if (false) {
    var d = 666;
  }
  console.log(d);
  console.log(b);
  var b = function () {};
  console.log(b);
  function c() {};
  console.log(c);
}
fn(1, 2);
```

**要想解决这道题需要知道，函数在预编译阶段都干了哪些事情，这就需要了解函数上下文中的变量对象的知识**

预编译->作用域的创建阶段/预编译的阶段->js的变量对象(AO对象：供js引擎自己去访问的)

预编译阶段具体步骤如下：
>1. 创建变量对象(AO对象：活动对象)
>2. 找形参和变量的声明，作为AO对象的属性名,值是undefined
>3. 实参和形参相统一
>4. 找函数声明，会覆盖掉变量的声明

1、创建变量对象
```js
AO = {

}
```

2、找形参和变量的声明，作为AO对象的属性名,值是undefined
```js
AO = {
  a: undefined,
  c: undefined,
  d: undefined,
  b: undefined,
}
```

3、实参和形参相统一
```js
AO = {
  a: 1,
  c: 2,
  d: undefined,
  b: undefined,
}
```

4、找函数声明，会覆盖掉变量声明
```js
AO = {
  a: function a() {},
  c: function c() {},
  d: undefined,
  b: undefined,
}
```

**接下来就是函数的执行阶段了，函数逐行执行**
```js
function fn(a, c) {
  console.log(a);//a此时的值是 function a() {}
  var a = 123;//a被赋值为123
  console.log(a);//123
  console.log(c);//function c(){}
  function a() {};
  console.log(d);//undefined
  if (false) {//没有执行该代码块里的代码，只有在预编译阶段进行了变量提升
    var d = 666;
  }
  console.log(d);//undefined
  console.log(b);//undefined
  var b = function () {};//b被赋值为function(){}
  console.log(b);//function(){}
  function c() {};
  console.log(c);//function c() {}
}
fn(1, 2);
```
