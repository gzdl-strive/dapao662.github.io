---
title: 浅谈instanceof和typeof
date: 2021-03-17 09:11:20
tag: js
toc: true
---

### typoe实现原理
typeof一般被用于判断一个变量的类型，我们可以利用typeof来判断number,string,object,boolean,function,undefined,symbol这七种类型，这种判断能帮助我们搞定一些问题，比如在判断不是object类型的数据的时候，typeof能比较清楚的告诉我们具体是哪一类的类型。但是，很遗憾的一点是，typeof在判断一个object的数据的时候只能告诉我们这个数据是object,而不能细致的具体到哪一种object,比如:
```js
let s = new String('rush');
console.log(typeof s);//object
console.log(s instanceof String);//true
```
要想判断一个数据具体是哪一种 object 的时候，我们需要利用 instanceof 这个操作符来判断，这个我们后面会说到。

来谈谈关于 typeof 的原理吧，我们可以先想一个很有意思的问题，js 在底层是怎么存储数据的类型信息呢？或者说，一个 js 的变量，在它的底层实现中，它的类型信息是怎么实现的呢？
其实，js 在底层存储变量的时候，**会在变量的机器码的低位1-3位存储其类型信息**
>* 000：对象
>* 010：浮点数
>* 100：字符串
>* 110：布尔
>* 1：整数

但是，对于undefined和null来说，这两个值的信息存储是有点特殊的
`null`: **所有机器码均为0**
`undefined`: 用`-2^30整数`来表示
所以，typeof在判断null的时候就出现问题了，由于null的所有机器码均为0，因此直接被当作了对象来看待

然而用instanceof来判断的话
```js
console.log(typeof null);//object
console.log(null instanceof null);//TypeError: Right-hand side of 'instanceof' is not an object
```
**null直接被判断为不是object,这也是javaScript的历史遗留bug**

>因此在用typeof来判断变量类型的时候，我们需要注意，最好使用typeof来判断基本数据类型(包括symbol),避免对Null的判断.

### Object.prototype.toString
还有一个不错的判断类型的方法，就是Object.prototype.toString,我们可以利用这个方法来对一个变量的类型进行比较准确的判断
```js
console.log(Object.prototype.toString.call(1));//'[object Number]'
console.log(Object.prototype.toString.call('hi'));//'[object String]'
console.log(Object.prototype.toString.call({ a: 'hi' }));//'[object Object]'
console.log(Object.prototype.toString.call([1, '66', true]));//'[object Array]'
console.log(Object.prototype.toString.call(true));//'[object Boolean]'
console.log(Object.prototype.toString.call(() => { }));//'[object Function]'
console.log(Object.prototype.toString.call(null));//'[object Null]'
console.log(Object.prototype.toString.call(undefined));//'[object Undefined]'
console.log(Object.prototype.toString.call(Symbol('fn')));//'[object Symbol]'
```

### instanceof操作符的实现原理
之前我们提到了instanceof来判断对象的具体类型，其实instanceof主要的作用就是判断一个实例是否属于某种类型
```js
let person = function () {
    ...
}
let nicole = new person();
console.log(nicole instanceof person);//true
```
当然，instanceof 也可以判断一个实例是否是其父类型或者祖先类型的实例
```js
let programmer = function () { }
programmer.prototype = new person();
let nicole2 = new programmer();
console.log(nicole2 instanceof programmer);//true
console.log(nicole2 instanceof person);//true
```

**这是 instanceof 的用法，但是 instanceof 的原理是什么呢？**
```js
function new_instanceof_of(leftValue, rightValue) {
    let rightProto = rightValue.prototype;//取右表达式的prototype值
    leftValue = leftValue.__proto__;//取左表达式的__proto__
    while (true) {
        if (leftValue === null) {
            return false;
        }
        if (leftValue === rightProto) {
            return true;
        }
        leftValue = leftValue.__proto__;
    }
}
```
>**其实instanceof主要的实现原理就是只要右边变量的prototype在左边变量的原型链上即可。**因此，instanceof在查找的过程中会遍历左边遍历的原型链，直到找到右边变量的prototype，
如果查找失败，则会返回false,告诉我们左边变量并非右边变量的实例.

### 看几个很有趣的例子
```js
function Foo() {}
console.log(Object instanceof Object);//true
console.log(Function instanceof Function);//true
console.log(Function instanceof Object);//true
console.log(Foo instanceof Foo);//false
console.log(Foo instanceof Object);//true
console.log(Foo instanceof Function);//true
```
要想全部理解 instanceof 的原理，除了我们刚刚提到的实现原理，我们还需要知道 JavaScript 的原型继承原理。
![1](/assets/jsImg/proto.jpg "继承")

我们知道每个 JavaScript 对象均有一个隐式的` __proto__ `原型属性，而显式的原型属性是 prototype，只有 `Object.prototype.__proto__` 属性在未修改的情况下为 `null` 值。
根据图上的原理，我们来梳理上面提到的几个有趣的 instanceof 使用的例子

#### Object instanceof Object
由图可知，Object的prototype属性是Object.prototype,而由于Object本身是一个函数,由Function所创建，所以Object.__proto__的值是Function.prototype,而Function.prototype的__proto__的值是Object.prototype,所以我们可以判断处，Object instanceof Object的结果是true.
用代码简单的表示一下
```js
leftValue = Object.__proto__ = Function.prototype
rightValue = Object.prototype
//第一次判断
leftValue != rightValue
leftValue = Function.prototype.__proto__ = Object.prototype
//第二次判断
leftValue === rightValue
//返回true
```
Function instanceof Function 和 Function instanceof Object 的运行过程与 Object instanceof Object 类似，故不再详说。

#### Foo instanceof Foo
Foo函数的prototype属性是Foo.prototype，而Foo的__proto__属性是Function.prototype.由图可知，Foo的原型链上并没有Foo.prototype,因此Foo instanceof Foo也就返回false
用代码简单表示一下
```js
leftValue = Foo.__proto__ = Function.prototype;
rightValue = Foo.prototype;
//第一次判断
leftValue !== rightValue
leftValue = Function.prototype.__proto__ = Object.prototype
//第二次判断
leftValue !== rightValue
left = Object.prototype.__proto__ = null
//第三次判断
leftValue !== rightValue
//第四次判断
leftValue === null
//返回 false
```

#### Foo instanceof Object
```js
leftValue = Foo.__proto__ = Function.prototype
rightValue = Object.prototype
//第一次判断
leftValue !== rightValue
leftValue = Function.prototype.__proto__ = Object.prototype
//第二次判断
leftValue === rightValue
// 返回 true
```

#### Foo instanceof Function
```js
leftValue = Foo.__proto__ = Function.prototype
rightValue = Function.prototype
//第一次判断
leftValue === rightValue
// 返回true
```

### 总结
简单来说，我们使用 typeof 来判断基本数据类型是 ok 的，不过需要注意当用 typeof 来判断 null 类型时的问题，如果想要判断一个对象的具体类型可以考虑用 instanceof，但是 instanceof 也可能判断不准确，比如一个数组，他可以被 instanceof 判断为 Object。所以我们要想比较准确的判断对象实例的类型时，可以采取 Object.prototype.toString.call 方法。