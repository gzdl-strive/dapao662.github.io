---
title: js例子
date: 2020-10-28 10:15:22
tags: js
---
## js的几个问题

### 问题一：浏览器的console里会打印出什么?
```js
var a = 10;
function foo() {
    console.log(a); // ??
    var a = 20;
}
foo();
```
**答案：undefined**
**解释：使用var关键字声明的变量被提升并在内存中为其赋值为undefined.但是初始化恰好发生在你在代码中写入它们的地方。另外，var声明的变量是函数作用域，而let和const是块作用域。所以，这就是这个所从的样子：**
```js
var a = 10; // 全局作用域
function foo() {
// 使用var声明的会被提升到函数作用域内顶部.
// 就像: var a;

console.log(a); // 打印 undefined

// 实际初始化值20只发生在这里
   var a = 20; // 本地 scope
}
```
### 问题二：如果是有const或let代替var，输出是否一样?
```js
var a = 10;
function foo() {
    console.log(a); // ??
    let a = 20;
}
foo();
```
**答案：ReferenceError: a is not defined**
**解释：let和const允许你声明一个变量被限制在一个块级作用域，或语句或表达式中。不像var，这些变量不会被提升，并且具有所谓的temporal dead zone(TDZ)。尝试在TDZ中访问这些变量将抛出一个ReferenceError，因为它们只能在执行到达声明才可被访问。**
```js
var a = 10; // 全局作用域
function foo() { // 进入新的作用域, TDZ开始

// 没有初始绑定的'a'被创建
    console.log(a); // ReferenceError

// TDZ 结束, 'a'只是在这里被初始化了一个值20
    let a = 20;
}
```
### 问题三："newArray"中的元素是什么?
```js
var array = [];
for(var i = 0; i <3; i++) {
 array.push(() => i);
}
var newArray = array.map(el => el());
console.log(newArray); // ??
```
**答案：[3,3,3]**
**解释：在for loop的头部声明一个带有var关键字的变量，为该变量创建一个绑定(存储空间)。**
```js
// 误解作用域:认为存在块级作用域
var array = [];
for (var i = 0; i < 3; i++) {
  // 三个箭头函数中的每个都引用同一个绑定，
  // 这就是为什么循环结束之后返回同样的数字3
  array.push(() => i);
}
var newArray = array.map(el => el());
console.log(newArray); // [3, 3, 3]
```
如果你声明一个具有块级作用域的变量，则会为每个循环迭代创建一个新绑定。
```js
// 使用ES6块级作用域绑定
var array = [];
for (let i = 0; i < 3; i++) {
  // 这一次，每个“i”引用一个特定迭代的绑定，并保留当时的值。
  // 因此，每个arrow函数返回一个不同的值。
  array.push(() => i);
}
var newArray = array.map(el => el());
console.log(newArray); // [0, 1, 2]
```
解决这个问题的另一种方法是使用闭包
```js
let array = [];
for (var i = 0; i < 3; i++) {
  array[i] = (function(x) {
    return function() {
      return x;
    };
  })(i);
}
const newArray = array.map(el => el());
console.log(newArray); // [0, 1, 2]
```
>为啥let可以,可以参考[这篇文章](https://github.com/xiaohesong/TIL/blob/master/front-end/es6/understanding-es6/Block-Binding.md "for循环使用let")
### 问题四：如果我们在浏览器控制台中运行'foo'函数，是否会导致堆栈溢出错误？
```js
function foo() {
  setTimeout(foo, 0); // will there by any stack overflow error?
};
```
**答案：不会**
解释：JavaScript并发模型基于“事件循环”。当我说“浏览器是JS的家(归宿)”时，我真正的意思是浏览器提供运行时环境来执行我们的JavaScript代码。浏览器的主要组件包括**调用堆栈 ，事件循环 ，任务队列 和 Web API**。像`setTimeout`，`setInterval`和`Promise`这样的全局函数不是JavaScript的一部分，而是`Web API`的一部分。JavaScript环境的可视化表示如下所示:
![image](/assets/jsImg/1.jpg "image")
>JS调用堆栈是后进先出`(LIFO)`。引擎一次从堆栈中获取一个函数，并从上到下依次运行代码。每次遇到一些异步代码(如setTimeout)时，它都会将其交给Web API(箭头1)。因此，每当触发事件时，callback都会被发送到任务队列(箭头2)。Event Loop不断监视任务队列，并按照排队顺序一次处理一个callback。每当调用堆栈为空时，循环检索回调并将其放入堆栈(箭头3)进行处理。请记住，如果调用堆栈不为空，则事件循环不会将任何callbacks推送到堆栈。

现在，有了这些知识，让我们试着回答上述问题：
**步骤**
1.调用`foo()`将把`foo`函数放进**调用栈**。
2.在处理内部代码时，JS引擎遇到`setTimeout`。
3.然后它将`foo`回调移交给**WebAPI**(箭头1)并从函数返回。调用堆栈再次为空。
4.计时器设置为`0`，因此`foo`将被发送到**任务队列**(箭头2)。
5.因为，我们的**调用堆栈**是空的，**事件循环**将选择`foo回调`并将其推送到**调用堆栈**进行处理。
6.进程再次重复，堆栈不会溢出 。

### 问题五：如果我们在控制台中运行以下函数，页面的UI(tab页)是否仍然响应？
```js
function foo() {
  return Promise.resolve().then(foo);
};
```
**答案：不会**
**解释：**大多数时候，我看到开发人员假设在事件循环的蓝图中只有一个任务队列(笔: 也叫`task queue`或`event queue`或`callback queue`)。但事实并非如此。我们可以有多个任务队列。由浏览器选择任意的队列并在其中处理*callbacks*。
在高层次上来看，JavaScript中有宏任务和微任务。`setTimeout`回调是` macrotasks `而`Promise`回调是 `microtasks` 。主要的区别在于他们的执行仪式。宏任务在单个循环周期中一次一个地推入堆栈，但是微任务队列总是在执行返回到`event loop`(包括任何额外排队的项)之前清空。因此，如果你将这些项快速的添加到这个你正在处理的队列，那么你将永远在处理微任务。
>在执行返回事件循环之前，微任务队列总是被清空

现在，当你在控制台中运行以下代码段时：
```js
function foo() {
  return Promise.resolve().then(foo);
};
```
每次调用'foo'都会继续在微任务队列上添加另一个'foo'回调，因此事件循环无法继续处理其他事件(`scroll`，`click`等)，直到该队列完全清空为止。因此，它会阻止渲染。
### 问题六：我们可以在不引起TypeError的情况下以某种方式使用以下语句的扩展语法吗？
```js
var obj = { x: 1, y: 2, z: 3 };
[...obj]; // TypeError
```
**答案：可以, 通过是对象iterables**
**解释：拓展运算符和`for-of`语句迭代`iterable`对象。数组或Map是具有默认迭代行为的内置`iterable`。对象不是可迭代的，但你可以使用`iterable`和`iterator协议`使它们可迭代。**
在Mozilla文档中，如果一个对象实现了@@iterator方法，那么它就是可迭代的，这意味着这个对象(或者它原型链上的一个对象)必须有一个带有@@iterator键的属性，这个键可以通过常量Symbol.iterator获得。
上述陈述可能看起来有点冗长，但下面的例子会更有意义：
```js
var obj = { x: 1, y: 2, z: 3 };
obj[Symbol.iterator] = function() {

  return {
    next: function() {
      if (this._countDown === 3) {
        const lastValue = this._countDown;
        return { value: this._countDown, done: true };
      }
      this._countDown = this._countDown + 1;
      return { value: this._countDown, done: false };
    },
    _countDown: 0
  };
};
[...obj]; // will print [1, 2, 3]
```
你还可以使用generator函数来自定义对象的迭代行为：
```js
var obj = { x: 1, y: 2, z: 3 };
obj[Symbol.iterator] = function*() {
  yield 1;
  yield 2;
  yield 3;
};
[...obj]; // print [1, 2, 3]
```
### 问题七：运行以下代码片段时，控制台上会打印什么？
```js
var obj = { a: 1, b: 2 };
Object.setPrototypeOf(obj, {c: 3});
Object.defineProperty(obj, 'd', { value: 4, enumerable: false });

// what properties will be printed when we run the for-in loop?
for(let prop in obj) {
    console.log(prop);
}
```
>`Object.setPrototypeOf`方法是针对**对象实例的**，而不是构造函数（类），此方法修改的是对象实例的内部属性`[prototype]`，也就是`_proto_`属性所指向的对象，它只是修改了特定对象上的原型对象，对于构造函数的`prototype`指向的原型对象没有影响。那是不是此方法就不能针对构造函数了，因为构造函数本身也是function(类)的实例。

**语法结构：**
Obejct.setPrototypeOf(obj,proto);
参数解析:
(1).obj：必需，对其设置原型的对象。
(2).proto:必需，新的原型对象。

>`Object.defineProperty()` 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

**语法结构：**
Object.defineProperty(obj, prop, descriptor);
obj:要定义属性的对象。
prop:要定义或修改的属性的名称或 Symbol 。
descriptor:要定义或修改的属性描述符。
返回值-->被传递给函数的对象。
**答案：a,b,c**
**解释: `for-in`循环遍历对象本身的可枚举属性以及对象从其原型继承的属性。可枚举属性是可以在`for-in`循环期间包含和访问的属性。**
```js
var obj = { a: 1, b: 2 };
var descriptor = Object.getOwnPropertyDescriptor(obj, "a");
console.log(descriptor.enumerable); // true
console.log(descriptor);
// { value: 1, writable: true, enumerable: true, configurable: true }
```
现在掌握了这些知识，应该很容易理解为什么我们的代码会打印出这些特定的属性：
```js
var obj = { a: 1, b: 2 }; // a, b are both enumerables properties


Object.setPrototypeOf(obj, { c: 3 });

Object.defineProperty(obj, "d", { value: 4, enumerable: false });

for (let prop in obj) {
  console.log(prop);
}
```

### 问题八：xGetter()将输出什么值?
```js
var x = 10;
var foo = {
  x: 90,
  getX: function() {
    return this.x;
  }
};
foo.getX(); // prints 90
var xGetter = foo.getX;
xGetter(); // prints ??
```
**答案：10**
**解释：**当我们将x初始化为全局作用域时，它将成为window对象的属性（假设它是浏览器环境而不是严格模式）。看下面代码：
```js
var x = 10; // 全局作用域
var foo = {
  x: 90,
  getX: function() {
    return this.x;
  }
};
foo.getX(); // prints 90
let xGetter = foo.getX;
xGetter(); // prints 10
```
我们可以断言：
```js
window.x === 10; // true
```
**this**将始终指向调用该方法的对象。因此，在`foo.getX()`的情况下，`this `指向`foo`对象返回值90。而在`xGetter()`的情况下，`this `指向`window` 对象返回值10。
要检索`foo.x`的值，我们可以通过使用`Function.prototype.bind`将 `this` 的值绑定到`foo`对象来创建新函数。
```js
let getFooX = foo.getX.bind(foo);
getFooX(); // prints 90
```

作者：xiaohesong
链接：https://www.jianshu.com/p/aec200057e33
来源：简书