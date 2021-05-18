---
title: 创建对象的多种方式
date: 2021-04-14 14:56:20
tag: js
toc: true
---

### 1、工厂模式
```js
function createPerson(name) {
    let o = new Object();
    o.name = name;
    o.getName = function () {
        console.log(this.name);
    }
    return o;
}
let person1 = createPerson('kevin');
console.log(person1.name);//kevin
person1.getName();//kevin
```
>缺点：对象无法识别，因为所有的实例都指向一个原型 ==>工厂函数 创建多个对象--(不方便判断类型)

### 2、构造函数模式
```js
function Person(name) {
    this.name = name;
    this.getName = function () {
        console.log(this.name);
    }
}
let person2 = new Person('simple');
console.log(person2.name);//simple
person2.getName();//simple
console.log(person2 instanceof Person);//true
```
>构造函数--名字大写开头，使用new调用--构造对象的函数
>1、会在内存中创建一个空对象
>2、设置构造函数的this,让this指向刚刚创建好的对象
>3、执行构造函数中的代码
>4、返回对象

>优点：实例可以识别为一个特定的类型
>缺点：每次创建实例时，每个方法都要被创建一次(上面例子中的getName方法会被多次创建)

### 2.1、构造函数模式优化
```js
function Person(name) {
    this.name = name;
    this.getName = getName;
}
function getName() {
    console.log(this.name);
}
let person3 = new Person('electronic');
```
>解决多个sayHi存储多份的问题--可以把sayHi变成全局的
>优点：解决了每个方法都要被重新创建的问题
>缺点：（这叫啥封装……）麻烦：每用一个方法写一遍--解决方法--原型

### 3、原型模式
```js
function Person(name) {
    
}
Person.prototype.name = 'Boomb14';
Person.prototype.getName = function () {
    console.log(this.name);
}
let person4 = new Person();
```
>优点：方法不会重新创建
>缺点：1. 所有的属性和方法都共享 2. 不能初始化参数

### 3.1原型模式优化
```js
function Person(name) {

}
Person.prototype = {
    name: 'perfector',
    getName: function () {
        console.log(this.name);
    }
}
let person5 = new Person();
```
>优点：封装性好了一点
>缺点：重写了原型，丢失了constructor属性

### 3.2、原型模式优化
```js
function Person() {

}
Person.prototype = {
    constructor: Person,
    name: 'flami',
    getName: function () {
        console.log(this.name);
    }
};
let person6 = new Person();
```
>优点：实例可以通过constructor属性找到所属构造函数
>缺点：原型模式该有的缺点还是有(1. 所有的属性和方法都共享 2. 不能初始化参数)

### 4、组合模式
**构造函数模式与原型模式双剑合璧**
```js
function Person(name) {
    this.name = name;
}
Person.prototype = {
    constructor: Person,
    getName: function () {
        console.log(this.name);
    }
}
let person7 = new Person('simple');
```
>优点：该共享的共享，该私有的私有，使用最广泛的方式
>缺点：有的人就是希望全部都写在一起，即更好的封装性

### 4.1动态原型模式
```js
function Person(name) {
    this.name = name;
    if (typeof this.getName !== 'function') {
        Person.prototype.getName = function () {
            console.log(this.name);
        }
    }
}
```
>动态原型模式它把所有信息都封装在了构造函数中，而通过在构造函数中初始化原型（仅在必要的情况下），又保持了同时使用构造函数和原型的优点。换句话说，可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。

>if语句检查的可以是初始化之后应该存在的任何属性或方法——不必用一大堆if语句检查每个属性和每个方法；只要检查其中一个即可。对于采用这种模式创建的对象，还可以使用instanceof操作符确定它的类型。
>**注意：使用动态原型模式时，不能用对象字面量重写原型**

解释下为什么：
```js
function Person(name) {
    this.name = name;
    if (typeof this.getName !== 'function') {
        Person.prototype = {
            constructor: Person,
            getName: function () {
                console.log(this.name);
            }
        }
    }
}
let person1 = new Person('simple');
let person2 = new Person('electronic');

person1.getName();// 报错 并没有该方法
person2.getName();// 注释掉上面的代码，这句是可以执行的。
```
为了解释这个问题，假设开始执行`let person8 = new Person('simple')`。
>我们回顾下 new 的实现步骤：
>1.首先新建一个对象
>2.然后将对象的原型指向 Person.prototype
>3.然后 Person.apply(obj)
>4.返回这个对象
>注意这个时候，回顾下apply的实现步骤，会指向obj.Person方法，这个时候就会执行if里面的内容，注意构造函数的prototype属性执行了实例的原型，使用字面量方式直接覆盖了Person.prototype,并不会更改实例的原型的值，person1依然指向了以前的原型，而不是Person.prototype。而之前的原型是没有getName方法的，所以就报错了

>**自己的理解：**当obj的原型指向Person.prototype（**未改变之前**）后，调用Person.apply,而这时就会执行if里面的内容，**此时Person.ptototype改变了**，但是字面量方式是直接覆盖Person.prototype,没有修改实例的原型的值(obj的原型指向的Person.prototype没有改变),所以后面调用getName时，因为实例里没有getName方法，就会报错

如果你就是想用字面量方式写代码，可以尝试下这种：
```js
function Person(name) {
    this.name = name;
    if (typeof this.getName !== 'function') {
        Person.prototype = {
            constructor: Person,
            getName: function () {
                console.log(this.name);
            }
        }
        return new Person(name);//新增-》用new又执行了一遍，这回实例就指向了正确的原型
    }
}
var person1 = new Person('kevin');
var person2 = new Person('daisy');

person1.getName(); // kevin
person2.getName();  // daisy
```

>上一篇文章 [类数组对象](/All/js/deepStudy/thirteen "类数组对象")
>下一篇文章 [继承的多种方式和优缺点](/All/js/deepStudy/fifteen "继承的多种方式和优缺点")