---
title: 重名问题下的变量提升
date: 2021-03-10 15:57:24
tag: js
---

### 函数名和 var 声明的变量重名
```js
var fn = 22;
function fn() {
    console.log('test');
}
console.log(fn);//22
fn();//TypeError: fn is not a function
```
>思路：带 var 声明的和带 function 声明的其实都是在 window 下的属性，也就是重名了，根据变量提升的机制，JS 代码自上而下执行时此时的 fn 还只是fn = 12，所以 fn() == 12() 又是一个类型错误 TypeError
![6](/assets/jsImg/variablePromotion/6.png "变量提升")

### 变量重名在变量提升阶段会重新定义也就是重新赋值
```js
console.log('1',fn())
function fn(){
    console.log(1)
}

console.log('2',fn())
function fn(){
    console.log(2)
}

console.log('3',fn())
var fn = '林一一'

console.log('4',fn())
function fn(){
    console.log(3)
}
/*
3
1 undefined
3
2 undefined
3
3 undefined
Uncaught TypeError: fn is not a function
*/
```
>思路：同样由于变量提升机制，fn 会被多次重新赋值最后赋值的地址值(假设为oxfffee)为最后一个函数，所以调用 fn都只是在调用最后一个函数输出都是 3， 代码执行到var fn = '林一一'，所以 fn() 其实 == 林一一() 导致类型错误 TypeError

![7](/assets/jsImg/variablePromotion/7.png "变量提升")

### 思考题
```js
var x = 2;
function x() {
    console.log(3);
}
console.log(typeof x);//number
```
**思路：这是一道比较简单的变量提升题，JS 代码自上而下执行时，a 被赋值成 2，输出就是 number 型**
![8](/assets/jsImg/variablePromotion/8.png "变量提升")

```js
console.log(fn);//[Function: fn]
var fn = 2021;
console.log(fn);//2021
function fn() {}
```
>变量提升：var fn = 2021；fn变量提升被赋值为undefined,然后funciton fn(){}, fn又被赋值为函数(带var的只声明还没有被定义，带function的已经声明和定义).此时console.log(fn);输出的就是函数.然后fn被赋值为2021,此时输出2021

