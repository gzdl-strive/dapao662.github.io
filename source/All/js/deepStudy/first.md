---
title: JS原型链
date: 2021-03-11 16:07:20
tag: js
toc: true
---

**构造函数创建对象**
```js
function Person() {}
const person = new Person();
person.name = 'hello';
console.log(person.name);//hello
```
在上面，Person是一个构造函数，我们使用new创建了一个实例对象person;

### prototype
>每个函数都有一个 prototype 属性
```js
Person.prototype.name = 'world';
const person2 = new Person();
console.log(person2.name);//world
```
那这个函数的 prototype 属性到底指向的是什么呢？是这个函数的原型吗？
其实，函数的 prototype 属性指向了一个对象，这个对象正是调用该构造函数而创建的实例的原型，也就是这个例子中的 person2 的原型。
那什么是原型呢？你可以这样理解：每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性。

### __proto__
>这是每一个JavaScript对象(除了 null )都具有的一个属性，叫__proto__，这个属性会指向该对象的原型。
```js
console.log(person.__proto__ === Person.prototype);//true
```

既然实例对象和构造函数都可以指向原型，那么原型是否有属性指向构造函数或者实例呢？

### constructor
>指向实例倒是没有，因为一个构造函数可以生成多个实例，但是原型指向构造函数倒是有的，这就要讲到第三个属性：constructor，每个原型都有一个 constructor 属性指向关联的构造函数。
```js
console.log(Person === Person.prototype.constructor);
//构造函数的实例可以使用原型对象上的任何属性
console.log(Person === person.constructor)//true
```
**顺便学一个ES5的方法,可以获得对象的原型**
```js
console.log(Object.getPrototypeOf(person) === Person.prototype);
```

### 实例与原型
>读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止。
```js
console.log(person.name);//hello
delete person.name;
console.log(person.name);//world
```

### 原型的原型
>原型也是一个对象，原型对象也有它的原型对象
```js
console.log(person.__proto__.__proto__)//{}
// Person构造函数的原型对象的原型是Object
console.log(person.__proto__.__proto__.__proto__);//null
```
Object构造函数的原型对象是null.null 表示“没有对象”，即该处不应该有值。所以 Object.prototype.__proto__ 的值为 null 跟 Object.prototype 没有原型，其实表达了一个意思。所以查找属性的时候查到 Object.prototype 就可以停止查找了。

### 补充
**constructor**
```js
console.log(person.constructor === Person);//true
```
当获取 person.constructor 时，其实 person 中并没有 constructor 属性,当不能读取到constructor 属性时，会从 person 的原型也就是 Person.prototype 中读取，正好原型中有该属性，所以：`person.constructor === Person.prototype.constructor`

**__proto__**
其次是 __proto__ ，绝大部分浏览器都支持这个非标准的方法访问原型，然而它并不存在于 Person.prototype 中，实际上，它是来自于 Object.prototype ，与其说是一个属性，不如说是一个 getter/setter，当使用 obj.__proto__ 时，可以理解成返回了 Object.getPrototypeOf(obj)。

>下一篇文章 [词法作用域和动态作用域](/All/js/deepStudy/second "词法作用域和动态作用域")