---
title: bind模拟实现
date: 2021-03-16 17:32:20
tag: js
toc: true
---

### bind
>一句话介绍 bind: `bind()` 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )
由此我们可以首先得出 bind 函数的两个特点：
>1. 返回一个函数
>2. 可以传入参数

### 返回函数的模拟实现
从第一个特点开始，举个例子：
```js
const foo = {
    value: 1
}
function bar() {
    console.log(this.value);
}
//返回一个函数
const bindFoo = bar.bind(foo);////[Function: bound bar]
bindFoo();//1
```
**关于指定 this 的指向，我们可以使用 call 或者 apply 实现，关于 call 和 apply 的模拟实现，可以查看《JavaScript深入之call和apply的模拟实现》。**
我们来写第一版的代码：

**第一版**
```js
Function.prototype.bind2 = function (context) {
    let self = this;
    return function () {
        return self.apply(context);
    }
}
```
>此外，之所以return self.apply(context),是考虑到绑定函数可能是有返回值的，依然是这个例子：
```js
var foo = {
    value: 2
}
function bar() {
    return this.value;
}
var bindFoo = bar.bind2(foo);
console.log(bindFoo());//2
```

### 传参的模拟实现
接下来看第二点，可以传入参数。这个就有点让人费解了，我在 bind 的时候，是否可以传参呢？我在执行 bind 返回的函数的时候，可不可以传参呢？让我们看个例子：
```js
var foo = {
    value: 2
}
function bar(name, age) {
    console.log(this.value);
    console.log(name);
    console.log(age);
}
var bindFoo = bar.bind(foo, 'kevin');
bindFoo(18);
/*
2
kevin
18
*/
```
函数需要传 name 和 age 两个参数，竟然还可以在 bind 的时候，只传一个 name，在执行返回的函数的时候，再传另一个参数 age!
这可咋办？不急，我们用 arguments 进行处理：

**第二版**
```js 
Function.prototype.bind2 = function (context) {
    let self = this;
    // 获取bind2函数从第二个参数到最后一个参数
    // slice接收两个参数start,end(如果end没有指定，则默认从start到末尾)
    let args = Array.prototype.slice.call(arguments, 1);
    return function() {
        // 这个时候的arguments是指bind返回的函数传入的参数
        let bindArgs = Array.prototype.slice(arguments);
        return self.apply(context, args.concat(bindArgs));
    }
}
```

### 构造函数效果的模拟实现
完成了这两点，最难的部分到啦！因为 bind 还有一个特点，就是一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。也就是说当 bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效。
举个例子：
```js
let value = 2;
let foo = {
    value: 1
}
function bar(name, age) {
    this.hobbit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}
bar.prototype.friend = 'kevin';
let bindFoo = bar.bind(foo, 'daisy');
let obj = new bindFoo(18);
/*
undefined
daisy
18
*/
console.log(obj.hobbit);//shopping
console.log(obj.friend);//kevin
```
注意：尽管在全局和 foo 中都声明了 value 值，最后依然返回了 undefind，说明绑定的 this 失效了，如果大家了解 new 的模拟实现，就会知道这个时候的 this 已经指向了 obj。
所以我们可以通过修改返回的函数的原型来实现，让我们写一下：

**第三版**
```js
Function.prototype.bind2 = function (context) {
    let self = this;
    let args = Array.prototype.slice.call(arguments, 1);
    let fBound = function () {
        let bindArgs = Array.prototype.slice.call(arguments);
        //当作为构造函数时，this指向示例，此时结果为true,将绑定函数的this指向该实例，可以让实例获得来自绑定函数的值
        // 以上面的是 demo 为例，如果改成 `this instanceof fBound ? null : context`，实例只是一个空对象，将 null 改成 this ，实例会具有 habit 属性
        // 当作为普通函数时，this 指向 window，此时结果为 false，将绑定函数的 this 指向 context
        return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    }
    // 修改返回函数的prototype为绑定函数的prototype,实例就可以继承绑定函数的原型中的值
    fBound.prototype = this.prototype;
    return fBound;
}
```

### 构造函数效果的优化实现
但是在这个写法中，我们直接将 fBound.prototype = this.prototype，我们直接修改 fBound.prototype 的时候，也会直接修改绑定函数的 prototype。这个时候，我们可以通过一个空函数来进行中转：

**第四版**
```js
Function.prototype.bind2 = function(context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fNop = function () {};

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fNop ? this : context, args.concat(bindArgs));
    }
    fNop.prototype = this.prototype;
    fBound.prototype = new fNop();
    return fBound;
}
```

### 小问题
调用 bind 的不是函数咋办？
```js
if (typeof this !== "function") {
    throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
}
```

### 最终代码
```js
Function.prototype.bind2 = function (context) {
    if (typeof this !== 'function') {
        throw new TypeError('Function.prototype.bind-what is trying to be bound is not callable')
    }
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var fNop = function () {};
    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fNop ? this : context, args.concat(bindArgs));
    }
    fNop.prototype = this.prototype;
    fBound.prototype = new fNop();
    return fBound;
}
```

### ES6写法
```js
Function.prototype.bind = function (context, ...args) {
    if (typeof this !== 'function') {
        throw new Error('Type Error');
    }
    //保存this的值
    var self = this;

    return function F() {
        //考虑new的情况
        if(this instanceof F) {
            return new self(...args, ...arguments);
        }
        return self.apply(context, [...args, ...arguments]);
    }
}
```

>上一篇文章 [call和apply模拟实现](/All/js/deepStudy/ten "call和apply模拟实现")
>下一篇文章 [new模拟实现](/All/js/deepStudy/twelve "new模拟实现")