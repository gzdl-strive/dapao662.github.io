---
title: ES6-Symbol
toc: true
date: 2021-11-15 17:37:02
---

## Symobl 基本使用

ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。它是 JavaScript 语言的第七种数据类型，是一种类似于字符串的数据类型。Symbol 特点:
(1) Symbol 的值是唯一的，用来解决命名冲突的问题
(2) Symbol 值不能与其他数据进行运算
(3) Symbol 定义的对象属性不能使用 for…in 循环遍历，但是可以使用`Reflect.ownKeys`来获取对象的所有键名

```js
//创建 Symbol
let s1 = Symbol();
console.log(s1, typeof s1);

//添加标识的 Symbol
let s2 = Symbol("尚硅谷");
let s2_2 = Symbol("尚硅谷");
console.log(s2 === s2_2); //false

//使用 Symbol for 定义
let s3 = Symbol.for("尚硅谷");
let s3_2 = Symbol.for("尚硅谷");
console.log(s3 === s3_2);

//不能与其他数据进行运算
let result = s + 100;
let result = s > 100;
let result = s + s;

// USONB  you are so niubility
// u  undefined
// s  string  symbol
// o  object
// n  null number
// b  boolean
```

<span style="color: red">注: 遇到唯一性的场景时要想到 Symbol</span>

**Symbol 创建对象属性**

```js
//向对象中添加方法 up down
let game = {
  name: "俄罗斯方块",
  up: function () {},
  down: function () {},
};
//声明一个对象
let methods = {
  up: Symbol(),
  down: Symbol(),
};

game[methods.up] = function () {
  console.log("我可以改变形状");
};

game[methods.down] = function () {
  console.log("我可以快速下降!!");
};

let youxi = {
  name: "狼人杀",
  [Symbol("say")]: function () {
    console.log("我可以发言");
  },
  [Symbol("zibao")]: function () {
    console.log("我可以自爆");
  },
};

console.log(youxi);
```

## Symbol内置值
除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值，指向语言内部使用的方法。可以称这些方法为魔术方法，因为它们会在特定的场景下自动执行。

| 方法 | 解释 |
|:------:|:-----:|
| Symbol.hasInstance | 当其他对象使用 instanceof 运算符，判断是否为该对象的实例时，会调用这个方法  |
| Symbol.isConcatSpreadable | 对象的 Symbol.isConcatSpreadable 属性等于的是一个布尔值，表示该对象用于 Array.prototype.concat()时，是否可以展开。  |
| Symbol.species | 创建衍生对象时，会使用该属性  |
| Symbol.match | 当执行 str.match(myObject) 时，如果该属性存在，会调用它，返回该方法的返回值。  |
| Symbol.replace | 当该对象被 str.replace(myObject)方法调用时，会返回该方法的返回值。  |
| Symbol.search | 当该对象被 str.search (myObject)方法调用时，会返回该方法的返回值。  |
| Symbol.split | 当该对象被 str.split(myObject)方法调用时，会返回该方法的返回值。  |
| Symbol.iterator | 对象进行 for...of 循环时，会调用 Symbol.iterator 方法，返回该对象的默认遍历器  |
| Symbol.toPrimitive | 该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。  |
| Symbol.toStringTag | 在该对象上面调用 toString 方法时，返回该方法的返回值  |
| Symbol. unscopables | 该对象指定了使用 with 关键字时，哪些属性会被 with环境排除。 |

 
 
 
