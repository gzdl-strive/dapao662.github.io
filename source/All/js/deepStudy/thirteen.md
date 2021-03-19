---
title: 类数组对象
date: 2021-03-18 14:59:20
tag: js
toc: true
---

### 介绍
>所谓类数组对象：是拥有一个length属性和若干索引属性的对象

举个例子：
```js
let array = ['name', 'age', 18];
let arrayLike = {
    0: 'name',
    1: 'age',
    2: 18,
    length: 3
}
```
即便如此，为什么叫做类数组对象呢？
那让我们从读写、获取长度、遍历三个方面看看这两个对象。
#### 读写
```js
console.log(array[0]);//name
console.log(arrayLike[0]);//name
array[0] = 'newName';
arrayLike[0] = 'newName';
console.log(array[0]);//newName
console.log(arrayLike[0]);//newName
```
#### 长度
```js
const arr = new Array(10);
console.log(arr.length);//10
console.log(array.length);//3
console.log(arrayLike.length);//3
```
#### 遍历
```js
for (let i = 0, len = array.length; i < len; i++) {
    //...
}
for (let i = 0, len = arrayLike.length; i < len; i++) {
    //...
}
```
是不是很像？
那类数组对象可以使用数组的方法吗？比如：
```js
arrayLike.push(5);//TypeError: arrayLike.push is not a function
```
所以终归还是类数组呐……

### 调用数组方法
如果类数组就是任性的想用数组的方法怎么办呢？
既然无法直接调用，我们可以用 `Function.call `间接调用：
```js
var arrayLike2 = {
    0: 'name',
    1: 'age',
    2: 'sex',
    length: 3
}
//call方法：改变this指向,执行函数
let ret = Array.prototype.join.call(arrayLike2, '&');
console.log(ret);//name&age&sex
let ret2 = Array.prototype.slice.call(arrayLike2, 0);
console.log(ret2);//[ 'name', 'age', 'sex' ]->slice可以做到类数组转数组
let ret3 = Array.prototype.map.call(arrayLike2, (item) => {
    return item.toUpperCase();
});
console.log(ret3);//[ 'NAME', 'AGE', 'SEX' ]
```

### 类数组转数组
在上面的例子中已经提到了一种类数组转数组的方法，再补充三个：
```js
var arrayLike3 = {
    0: 'name',
    1: 'age',
    2: 'sex',
    length: 3
}
//1、slice
let a1 = Array.prototype.slice.call(arrayLike3);
let a2 = Array.prototype.slice.call(arrayLike3, 0);
console.log(a1);//[ 'name', 'age', 'sex' ]
console.log(a2);//[ 'name', 'age', 'sex' ]
//2、splice
let a3 = Array.prototype.splice.call(arrayLike3, 0);
console.log(a3);//[ 'name', 'age', 'sex' ]
//3、ES6 Array.from
let a4 = Array.from(arrayLike3);
console.log(a4);//[ 'name', 'age', 'sex' ]
//4、apply
let a5 = Array.prototype.concat.apply([], arrayLike3);
let a6 = Array.prototype.concat.apply(arrayLike3, []);
console.log(a5);//[ 'name', 'age', 'sex' ]
console.log(a6);//[ { '0': 'name', '1': 'age', '2': 'sex', length: 3 } ]
```
那么为什么会讲到类数组对象呢？以及类数组有什么应用吗？
要说到类数组对象，Arguments 对象就是一个类数组对象。在客户端 JavaScript 中，一些 DOM 方法(`document.getElementsByTagName()`等)也返回类数组对象。

### Arguments对象
接下来重点讲讲 Arguments 对象。
Arguments 对象只定义在函数体中，包括了函数的参数和其他属性。
在函数体中，arguments 指代该函数的 Arguments 对象。
举个例子:
```js
function foo(name, age, sex) {
    console.log(arguments);
}
foo('simple', 18, 'man');
```
![1](/assets/jsImg/4.png "1")
我们可以看到除了类数组的索引属性和length属性之外，还有一个callee属性，接下来我们一个一个介绍。

#### length属性
Arguments对象的length属性，表示实参的长度，举个例子：
```js
function foo2 (b, c, d) {
    console.log('实参的长度:', arguments.length);
}
console.log('形参的长度:', foo2.length);
foo(1);
// 形参的长度为：3
// 实参的长度为：1
```
![2](/assets/jsImg/5.png "1")

#### callee属性
Arguments对象的callee属性，通过它可以调用函数自身
讲个闭包经典面试题使用 callee 的解决方法：
```js
var data = [];
for (var i = 0; i < 3; i++) {
    (data[i] = function () {
        console.log(arguments.callee.i);
    }).i = i;
}
data[0]();//0
data[1]();//1
data[2]();//2
```
接下来讲讲 arguments 对象的几个注意要点：

### arguments和对应参数的绑定
```js
function foo2 (name, age, sex, hobbit) {
    console.log(name, arguments[0]);//simple simple
    //改变实参
    name = 'new name';
    console.log(name, arguments[0]);//new name new name
    //改变arguments
    arguments[1] = 'new age';
    console.log(age, arguments[1]);//new age new age
    //测试未传入的是否会绑定
    console.log(sex);//undefined
    sex = 'new sex';
    console.log(sex, arguments[2]);//new sex undefined
    arguments[3] = 'new hobbit';
    console.log(hobbit, arguments[3]);//undefined new hobbit
}
foo2('simple', '18');
```
传入的参数，实参和 arguments 的值会共享，当没有传入时，实参与 arguments 值不会共享.除此之外，以上是在非严格模式下，如果是在严格模式下，实参和 arguments 是不会共享的。

### 传递参数
将参数从一个函数传递到另一个函数
```js
function foo3() {
    //foo3中this指向undefined=>window
    bar.apply(this, arguments);
}
function bar(a, b, c) {
    console.log(a, b, c);
}
foo3(1, 2, 3);//1 2 3
```

### 强大的ES6
使用ES6的 ... 运算符，我们可以轻松转成数组
```js
function func(...arguments) {
    console.log(arguments);//[1, 2, 3]
}
func(1, 2, 3);
```

### 应用
arguments的应用其实很多，在下个系列，也就是 JavaScript 专题系列中，我们会在 jQuery 的 extend 实现、函数柯里化、递归等场景看见 arguments 的身影。
如果要总结这些场景的话，暂时能想到的包括：
* 参数不定长
* 函数柯里化
* 递归调用
* 函数重载
* ...

>上一篇文章 [new模拟实现](/All/js/deepStudy/twelve "new模拟实现")
>下一篇文章 [创建对象的多种方式](/All/js/deepStudy/fourteen "创建对象的多种方式")
