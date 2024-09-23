---
title: 继承的多种方式和优缺点
date: 2021-04-14 15:15:20
tag: js
toc: true
---

### 1、原型链继承
```js
function Parent() {
    this.name = 'kevin';
}
Parent.prototype.getName = function () {
    console.log(this.name);
}

function Child() {

}
Child.prototype = new Parent();
let child1 = new Child();
child1.getName();//kevin
```
#### 问题：1、引用类型的属性被所有实例共享，举个例子
```js
function Parent() {
    this.names = ['kevin', 'daisy'];
}
function Child() {

}
Child.prototype = new Parent();
let child2 = new Child();
child2.names.push('simple');
console.log(child2.names);//[ 'kevin', 'daisy', 'simple' ]
let child3 = new Child();
console.log(child3.names);//[ 'kevin', 'daisy', 'simple' ]
```
#### 问题2：在创建 Child 的实例时，不能向Parent传参


### 2、借用构造函数(经典继承)
```js
function Parent() {
    this.names = ['simple', 'electronic'];
}
function Child() {
    Parent.call(this);
    /*
    自己的观点：此时的this指向的是这个child实例
    使用new操作符时，会创建一个对象(obj),obj的原型指向Child的原型对象，Child.apply(obj)=>(this此时指向的是obj这个对象),然后返回这个对象
    this此时指向的是obj这个对象 ==》 这个时候我们在Parent.call(this)=>相当于Parent.call(obj) => 所以会把结果都放到obj这个对象里，返回给实例child
    */
}
let child4 = new Child();
child4.names.push('boomb14');//[ 'simple', 'electronic', 'boomb14' ]
console.log(child4.names);
let child5 = new Child();
console.log(child5.names);//[ 'simple', 'electronic' ]
```
>优点：
>1、避免了引用类型的属性被所有实例共享
>2、可以在Child中向Parent传参

举个例子：
```js
function Parent(name) {
    this.name = name;
}
function Child(name) {
    Parent.call(this, name);
}
let child6 = new Child('simple');
console.log(child6.name);//simple
let child7 = new Child('electronic');
console.log(child7.name);//electronic
```
>缺点：方法都在构造函数中定义，每次创建实例都会创建一遍方法

### 3、组合继承:原型链继承和经典继承双剑合璧。
```js
function Parent(name) {
    this.name = name;
    this.colors = ['red', 'green', 'blue'];
}
Parent.prototype.getName = function () {
    console.log(this.name);
}

function Child(name, age) {
    Parent.call(this, name);
    this.age = age;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;

let child8 = new Child('simple', 25);
child8.colors.push('aqua');
console.log(child8.name);//simple
console.log(child8.age);//25
console.log(child8.colors);//[ 'red', 'green', 'blue', 'aqua' ]
child8.getName();//simple

let child9 = new Child('electronic', 23);
console.log(child9.name);//electronic
console.log(child9.age);//23
console.log(child9.colors);//['red', 'green', 'blue']
child9.getName();//electronic
```
>优点：融合了原型链继承和构造函数的优点，是javaScript中最常用的继承模式

### 4、原型式继承
```js
function createObj(o) {
    function F() {}
    F.prototype = o;
    return new F();
    /*
    new操作符操作步骤：
    1、创建一个对象obj
    2、obj的原型指向构造函数的原型对象(即F.prototype)
    3、指向F.apply(obj)
    4、返回obj
    ==>这边把构造函数F的原型对象指向了o,那么我们创建的obj的原型也指向了o
    */
}
```
>就是 `ES5 Object.create` 的模拟实现，将传入的对象作为创建的对象的原型。
>缺点：包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样.

```js
let person = {
    name: 'perfector',
    friends: ['boomb14', 'flami']
}
let person1 = createObj(person);
let person2 = createObj(person);

person1.name = 'person1';
console.log(person2.name);//perfector，修改person1.name的值，person

person1.friends.push('666');
console.log(person2.friends);//['boomb14', 'flami', '666']
```
>注意：修改person1.name的值，person2.name的值并未发生改变，并不是因为person1和person2有独立的name值，而是因为person1.name = 'person1'，给person1添加了name值，并非修改了原型上的name值

### 5、寄生式继承
创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。
```js
function createObj (o) {
    var clone = Object.create(o);
    clone.sayName = function () {
        console.log('hi');
    }
    return clone;
}
```
>缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

### 6、寄生组合式继承
为了方便大家阅读，在这里重复一下组合继承的代码：
```js
function Parent(name) {
    this.name = name;
    this.colors = ['red', 'green', 'blue'];
}
Parent.prototype.getName = function () {
    console.log(this.name);
}
function Child(name, age) {
    Parent.call(this, name);
    this.age = age;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;

let child10 = new Child('simple', 24);
console.log(child10);
```
>组合继承最大的缺点是会调用两次父构造函数。

**一次是设置子类型实例的原型的时候：**
```js
Child.prototype = new Parent();
```
**一次在创建子类型实例的时候：**
```js
var child1 = new Child('kevin', '18');
```
回想下 new 的模拟实现，其实在这句中，我们会执行：
```js
Parent.call(this, name);
```
在这里，我们又会调用了一次 Parent 构造函数。
所以，在这个例子中，如果我们打印 child10 对象，我们会发现 Child.prototype 和 child1 都有一个属性为colors，属性值为['red', 'blue', 'green']

**那么我们该如何精益求精，避免这一次重复调用呢？**
如果我们不使用` Child.prototype = new Parent()` ，而是间接的让 `Child.prototype` 访问到 `Parent.prototype` 呢？
看看如何实现：
```js
function Parent(name) {
    this.name = name;
    this.colors = ['red', 'green', 'blue'];
}
Parent.prototype.getName = function () {
    console.log(this.name);
}
function Child(name, age) {
    Parent.call(this, name);
    this.age = age;
}

//关键的三步
let F = function() {};
F.prototype = Parent.prototype;
Child.prototype = new F();

let child11 = new Child('simple', 24);
console.log(child11);
```

**最后我们封装一下这个继承方法**
```js
function object(o) {
    function F() {};
    F.prototype = o;
    return new F();
}
function prototype(child, parent) {
    let prototype = object(parent.prototype);
    prototype.constructor = child;
    child.prototype = prototype;
}
```

当我们使用的时候
```js
prototype(Child, Parent);
```
>引用《JavaScript高级程序设计》中对寄生组合式继承的夸赞就是：这种方式的高效率体现它只调用了一次 Parent 构造函数，并且因此避免了在 Parent.prototype 上面创建不必要的、多余的属性。与此同时，原型链还能保持不变；因此，还能够正常使用 instanceof 和 isPrototypeOf。开发人员普遍认为寄生组合式继承是引用类型最理想的继承范式。

>上一篇文章 [创建对象的多种方式](/All/js/deepStudy/fourteen "创建对象的多种方式")
