---
title: Object.create(null)
date: 2021-03-19 09:40:20
tag: js
toc: true
---

### Object.create(null)与{}的区别
我们通过例子来直观的认识下两者:
```js
let obj = {};
let obj2 = Object.create(null);
console.log(obj);
console.log(obj2);
```
![1](/assets/jsImg/6.png "对象字面量创建")
![2](/assets/jsImg/7.png "Object.create(null)")

>1. 通过字面量的方式定义对象，其原型指向 `Object.prototype`，也就是 `obj.__proto__ === Object.prototype`，同时包含了`toString,hasOwnProperty`等方法
>2. 通过`Object.create(null)`创建出来的对象是一个干净对象，除自身属性之外，没有附加其他属性

### 使用Object.create(null)的原因
很多框架源码作者使用它来初始化一个新的对象，难道是最佳实践？
1. 通过`Object.create(null)`创建出来的对象，没有任何属性，显示`No properties`。我们可以将其当成一个干净的` map `来使用，自主定义 `toString,hasOwnProperty`等方法，并且不必担心将原型链上的同名方法被覆盖。
2. {...}创建的对象，使用`for in`遍历对象的时候，会遍历原型链上的属性，带来性能上的损耗。使用`Object.create(null)`则不必再对其进行遍历了
框架源码对性能的要求，哪怕是一点点，都值得关注

### 手写Object.create
```js
//create不是在原型上的，直接判断是否有这个方法
if (typeof Object.create !== 'function') {
    Object.create = function(proto, properties) {
        //判断类型，第一个参数传入的必须是object，function
        if (typeof proto !== "object" && typeof proto !== "function") {
            throw new TypeError("Object prototype may only be an Object: " + proto);
        }
        //简单的实现过程,忽略了properties
        var func = function () {};
        func.prototype = proto;//将func的原型指向传入的proto
        return new func();//返回创建的新对象，这里思考下，new func()又做了什么事情呢
    }
}
```

### new func()做了什么
new func()的作用是创建一个新的对象，其中func是一个构造函数，在这个过程中，主要包含了如下步骤：
1. 创建空对象obj
2. 将obj的原型设置为构造函数的原型
3. 将obj为上下文执行构造函数,func.call(obj)
4. 返回obj对象
根据上面的过程，我们通过代码来模仿new的实现原理：
因为new是关键字，无法像 bind,call,apply 等函数一样直接覆盖，我们写一个函数，命名为 objectCreator。使用的时候是这样的:
**注意：此处new实现只是模拟`new func()`-->不带参数**
```js
function Person() {
    ...
}
//使用new
var f = new Person();
//使用objectCreator
var f = objectCreator(Person)
```
实现方案如下：
```js
function objectCreator(func) {
    //创建一个空对象，并将空对象的原型设置为构造函数的原型
    var obj = Object.create(func.prototype);
    //以obj为上下文执行构造函数
    var newObj = func.call(obj);
    //如果构造函数有返回结果，则返回，若无直接返回obj
    if (typeof newObj === 'object') {
        return newObj;
    } else {
        return obj;
    }
}
```