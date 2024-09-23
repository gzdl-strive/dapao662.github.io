---
title: 继承
date: 2021-09-03 17:24:02
toc: true
---

### 1、原型链继承
```js
function Parent() {
  this.name = ['kevin'];
}
Person.prototype.getName = function() {
  console.log(this.name);
}

function Child(){}
Child.prototype = new Parent();//子类的原型指向父类的实例
let child1 = new Child();
let child2 = new Child();
child1.getName();//['kevin']
child1.getName();//['kevin']

// 修改实例child1的name
child1.name.push('child1');
child1.getName();//['kevin', 'child1']
child1.getName();//['kevin', 'child1']
```

**缺点：引用类型的属性被所有实例共享**
**问题: 而且还存在一个问题：我们在创建Child实例时，不能像Parent传参**

### 2、构造函数继承(经典继承)
```js
function Parent(name) {
  this.name = [name];
}
Parent.prototype.getName = function () {
  console.log(this.name);
}
function Child(name) {
  Parent.call(this, name)
}
let child1 = new Child('simple');
let child2 = new Child('boomb14');

console.log(child1.name);//[ 'simple' ]
console.log(child2.name);//[ 'boomb14' ]

child1.name.push('electronic');

console.log(child1.name);//[ 'simple', 'electronic' ]
console.log(child2.name);//[ 'boomb14' ]

// **注意如下代码**
child1.getName();//报错 
```
>优点：
>1、避免了引用类型的属性被所有实例共享
>2、可以在Child中向Parent传参

**缺点：访问不到父类中的方法**

### 3、组合继承
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
**缺点：每创建一个child时都会调用`Parent.call`和`new Parent()`**,会调用两次父构造函数

- 一次是设置子类型实例的原型的时候
```js
Child.prototype = new Parent()
```

- 一次在创建子类型实例的时候
```js
var child1 = new Child('kevin', '18');
```
回想下 new 的模拟实现，其实在这句中，我们会执行：
```js
Parent.call(this, name);
```
在这里，我们又会调用了一次 Parent 构造函数。

### 4、寄生式组合继承
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
//不同之处
Child.prototype = Parent.prototype;
Child.prototype.constructor = Child;
```
**缺点：因为`Child.prototype = Parent.prototype`指向同一个对象，所以子类改变了该对象，相应的的父类也会改变**

解决: 拷贝一份Parent.prototype

### 5、寄生式组合继承(优化)
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
//不同之处
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```



