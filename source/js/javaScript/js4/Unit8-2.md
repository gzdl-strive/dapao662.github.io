---
title: 第八章-对象、类与面向对象编程
date: 2020-12-19 20:05:10
tag: js
---

## 原型模式
>1、`isPrototypeOf()`方法
```js
function Person() {}
Person.prototype.name = 'zero';
let person1 = new Person();
console.log(Person.prototype.isPrototypeOf(person1));//true--通过原型对象调用isPrototypeOf()方法检查person1,里面有链接指向Person.prototype
```
>2、`Object.getPrototypeOf()`方法--返回参数的内部特性[[prototype]]的值
```js
console.log(Object.getPrototypeOf(person1));//Person { name: 'zero' }
```
>3、`hasOwnProperty()`确定某个属性是在原型还是实例上
```js
console.log(person1.hasOwnProperty('name'));//false;
person1.name = "索隆";
console.log(person1.hasOwnProperty('name'));//true
```
>4、in操作符-会在可以通过对象访问指定属性时返回true-无论该属性是在原型还是实例上
```js
let person2 = new Person();
console.log('name' in person2);//true--原型name
```

**如果想确定某个属性是否存在于原型属性上--结合`hasOwnProperty`和`in操作符`**
```js
function hasOwnPropertyPropery(object, name) {
    return !object.hasOwnProperty(name) && (name in object)
}
console.log(hasOwnPropertyPropery(person1, 'name'));//false--person1实例上有name属性
console.log(hasOwnPropertyPropery(person2, 'name'));//true--person2实例没有name属性
```

## 对象迭代
>`Object.values()`和`Object.entries()`-用于将对象内容转换为序列化的-更重要的是可迭代的-格式
```js
// Object.values()-返回对象值的数组
const o = {
    name: '大O',
    age: 18,
    sex: 'man',
    child: {}
};
console.log(Object.values(o));//[ '大O', 18, 'man', {} ]
// Object.entries()-返回键/值对的数组
console.log(Object.entries(o));//[ [ 'name', '大O' ], [ 'age', 18 ], [ 'sex', 'man' ], [ 'child', {} ] ]
//注意非字符串属性会被转换成字符串输出


console.log(Object.getOwnPropertyDescriptors(Person.prototype));//原生constructor属性不可枚举(enumerable为false)
/*
{
    constructor: {
      value: [Function: Person],
      writable: true,
      enumerable: false,
      configurable: true
    },
    name: {
      value: 'zero',
      writable: true,
      enumerable: true,
      configurable: true
    }
  }
*/
```

### 1、其他原型语法-每次定义都会重写一遍Person.prototype--封装起来
```js
Person.prototype = {
    name: 'hello',
    age: 18,
    job: 'web前端',
    sayName() {
        console.log(this.name);
    }
}
```
**存在问题 `Person.prototype`的`constructor属性`不指向`Person`了,如下：**
```js
let friend = new Person();
console.log(friend instanceof Person);//true
console.log(friend instanceof Object);//true
// 但是
console.log(friend.constructor == Person);//false-constructor属性不指向Person了
console.log(friend.constructor == Object);//true
// 改进
Person.prototype = {
    constructor: Person,
    name: 'hello',
    age: 18,
    job: 'web前端',
    sayName() {
        console.log(this.name);
    }
}
let friend2 = new Person();
console.log(friend2.constructor == Person);//true
console.log(friend2.constructor == Object);//false
console.log(Object.getOwnPropertyDescriptors(Person.prototype));//修后的constructor的变成可枚举
/*
  constructor: {
    value: [Function: Person],
    writable: true,
    enumerable: true,
    configurable: true
  }
*/
```
**再改进-恢复`constructor属性`-使用`Object.defineProperty()`定义**
```js
Person.prototype = {
    name: 'hello',
    age: 18,
    job: 'web前端',
    sayName() {
        console.log(this.name);
    }
}
//恢复constructor属性
Object.defineProperty(Person.prototype, 'constructor', {
    enumerable: false,
    value: Person
});
let friend3 = new Person();
console.log(friend3.constructor == Person);//true
console.log(Object.getOwnPropertyDescriptors(Person.prototype));
/*
  constructor: {
    value: [Function: Person],
    writable: false,
    enumerable: false,
    configurable: false
  }
*/
```

### 2、原型的动态性
```js
function People() {};
let p1 = new People();
People.prototype = {
    constructor: People,
    name: 'abc',
    age: 20,
    sayName() {
        console.log(this.name);
    }
}
p1.sayName();//p1.sayName is not a function--People的实例在重写原型对象之前创建，调用出错原因在于p1指向的原型还是最初的原型，而那个原型上没有sayName方法。
```
**重写构造函数上的原型之后再创建的实例才会引用新的原型。**

### 3、原生对象原型
```js
console.log(Array.prototype.sort);//[Function: sort]
console.log(String.prototype.substring);//[Function: substring]
//* 通过原生对象的原型可以取得所有默认方法的引用，也可以给原生原型的实例定义新的方法。
//给String原始值包装类型的实例添加一个方法
String.prototype.startsWith = function(text) {
    return this.indexOf(text) === 0 ? `gnls:${true}` : `gnls:${false}`
}
let msg = 'hello world';
console.log(msg.startsWith('hello'));//gnls:true
console.log(msg.startsWith('llo'));//gnls:false
```

