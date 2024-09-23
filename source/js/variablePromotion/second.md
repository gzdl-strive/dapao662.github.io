---
title: 带var和不带var的区别
date: 2021-03-10 15:32:24
tag: js
---

>1、全局作用域中不带var声明变量虽然也可以但是建议带上 var声明变量，不带 var 的相当于给window对象设置一个属性罢了。
2、私有作用域(函数作用域)，带 var 的是私有变量。不带 var 的是会向上级作用域查找，如果上级作用域也没有那么就一直找到 window 为止，这个查找过程叫作用域链。
3、全局作用域中使用 var 申明的变量会映射到 window 下成为属性。

```js
c = 12;
console.log(c);//12
console.log(window.c)//12
```
**注意**
```js
var d = e = 12;//这里的e也是不带var的
//相当于
//var d = 12; 
//e = 12;
```

### 思考
**测试1**
```js
console.log(a, b);//undefined, undefined
var a = 12, b = 'Test';
function foo() {
    console.log(a, b);//undefined Test -->a是私有变量，b没有使用var,所以向上作用域查找
    var a = b = 13;//相当于var a = 13; b = 13;(这里a是函数的私有变量，b是全局的变量相当于重新赋值了)
    console.log(a, b);//13 13
}
foo();
console.log(a, b);//12 13
```

**测试2**
```js
console.log(a, b);//undefined, undefined
var a = 12, b = '测试';
function foo() {
    console.log(a, b);//12 测试   -->这里a,b没有使用var定义，所以需要向上级作用域查找
    //var a = b = 13;
    console.log(a, b);//12测试
}
foo();
console.log(a, b);//12 测试
```

**测试3**
```js
a = 0;//window.a = 0
function foo() {
    var a = 12;
    b = 'ceshi';
    console.log('b' in window);true
    console.log(a, b);//12 ceshi
}
foo();
console.log(b);//ceshi
console.log(a);//0
```
**这是比较简单的一道题，需要注意的是函数内的 b 没有带 var，b 会一直向上查找到 window 下，mie发现 window 下也没有就直接给 window 设置了一个属性 window.b = 'ceshi'，同理全局下的 a 也一样。**

**测试4**
```js
function foo() {
    console.log(a);//ReferenceError: a is not defined
    a = 12;
    b = 'Test';
    console.log('b' in window);
    console.log(a, b);
}
foo();
```
**问题4和问题3的主要区别在于第一个 console.log(a) 处，因为 a 不在函数作用域内，就会向上找 window 下的作用域，发现也没有就会直接抛出引用错误 ReferenceError**

**经典面试题**
```js
foo();
console.log(v1);//ReferenceError: v1 is not defined
console.log(v2);//2021
console.log(v3);//2021
function foo() {
    var v1 = v2 = v3 = 2021;//相当于var v1 = 2021; v2 = 2021; v3 = 2021;(v1是函数私有变量，v2,v3因为没有使用var定义就向上找window下的作用域，发现 window 下也没有就直接给 window 设置了window.v2 = 2021; window.v3 = 2021;）
    console.log(v1);//2021
    console.log(v2);//2021
    console.log(v3);//2021
}
```