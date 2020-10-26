---
title: 第三章
date: 2020-10-26 21:22:10
tag: js
---

### 变量
>ECMAScript的变量是松散类型的
```js
var message = "hi";
message = 100;
function test() {
    var message = "hi";
}
test();
console.log(message);
```
输出结果:
`message is not defined`
原因是`var `操作符定义的变量将成为定义该变量的作用域中的局部变量也就是说
如果在函数中使用var定义一个变量，那么这个变量在函数退出后就会被**销毁**
-->可以省略`var`操作符-->把`message`变成一个全局变量-->(不推荐使用,**因为在局部作用域定义的全局变量很难维护**)
严格模式下，不能定义名为`eval或argument`的变量

### 数据类型
>`ECMAScript`中有五种简单数据类型（基本数据类型）:`undefined、Null、Boolean、Number、String`
>还有一种复杂数据类型`Object`-本质上由一组无序的名值对组成。

`typeof`操作符--`ECMAScript是`松散类型的，需要使用typeof来检测变量的数据类型
```js
"undefined"//如果这个值未定义
"boolean"//布尔值
"string"//字符串
"number"//数值
"object"//对象或者null
"function"//函数
```
console.log(typeof null)**返回值`object`特殊值null被认为是一个空对象的引用**

**`undefined`类型--只有一个值，即`undefined`--使用`var`声明变量但未初始化变量就为`undefined`**
```js
var message;
console.log(message == undefined)//true
console.log(typeof message);//未初始化返回undefined
console.log(typeof age);//未声明变量也返回undefined
```

### 一元操作符

**加操作符**
放在数值前面不会对数值产生任何影响
```js
const a = 20;
console.log(+a);//20
```
**不过，在对非数值类型时**
```js
let s1 = "01";
let s2 = "1.1";
let s3 = "z";
let b = false;
let o = {
    valueOf: function() {
        return 1;
    }
}
console.log(+s1);//1
console.log(+s2);//1.1
console.log(+s3);//NaN
console.log(+b);//0
console.log(+o);//1
```

**减操作符--表示负数**

>`ECMAScript`函数不能像传统意义上那样实现重载,如果在`ECMAScript`中定义了两个名字相同的函数，则该名字只属于后定义的函数。

**总结**
* 1、ECMAScript中基本数据类型包括：Undefined、Null、Boolean、Number、和String
* 2、和其他语言不同的是，ECMAScript没有为整数和浮点数分别定义不同的数据类型，Number类型可以用于表示所有数值
* 3、ECMAScript中也有一种复杂的数据类型，即Object类型,该类型是这门语言中所有对象的基础类型。
* 4、严格模式为这门语言中容易出错的地方施加了限制.
* 5、ECMAScript提供了很多与C及其他类C语言中相同的基本操作符，包括算术操作符、布尔操作符、关系操作符、相等操作符、及赋值操作符等。
* 6、ECMAScript从其它语言中借鉴了很多流控制语句，例如if语句、for语句和switch语句等，ECMAScript中的函数与其他语言中的函数有诸多不同之处。
* 7、无须指定函数的返回值，因为任何ECMAScript函数都可以在任何时候返回任何值
8、实际上，未指定返回值的函数返回的是一个特殊的Undefined值
* 9、ECMAScript中也没有函数签名的概念，因为其函数参数是以一个包含零或多个值的数组的形式传递的
* 10、可以向ECMAScript函数传递任意数量的参数，并且可以通过arguments对象来访问这些参数。
* 11、由于不存在函数签名的特性，ECMAScript函数不能重载。
