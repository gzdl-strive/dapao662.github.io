---
title: 闭包
date: 2021-03-12 17:27:20
tag: js
toc: true
---

### 定义
>MDN 对闭包的定义为：**闭包是指那些能够访问自由变量的函数。**

那什么是自由变量呢？
**自由变量是指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量。**
由此，我们可以看出闭包共有两部分组成：**闭包 = 函数 + 函数能够访问的自由变量**

举个例子
```js
var a = 1;
function foo() {
    console.log(a);
}
foo();
```
foo 函数可以访问变量 a，但是 a 既不是 foo 函数的局部变量，也不是 foo 函数的参数，所以 a 就是自由变量。
那么，函数 foo + foo 函数访问的自由变量 a 不就是构成了一个闭包嘛……
还真是这样的！
所以在《JavaScript权威指南》中就讲到：**从技术的角度讲，所有的JavaScript函数都是闭包**。
咦，这怎么跟我们平时看到的讲到的闭包不一样呢！？
别着急，这是**理论上的闭包**，其实还有一个**实践角度上的闭包**，让我们看看汤姆大叔翻译的关于闭包的文章中的定义：
ECMAScript中，闭包指的是：
1. 从理论角度：所有的函数。
因为它们都在创建的时候就将上层上下文的数据保存起来了。哪怕是简单的全局变量也是如此，因为函数中访问全局变量就相当于是在访问自由变量，这个时候使用最外层的作用域。
2. 从实践角度：以下函数才算是闭包：
    * (1)即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）
    * (2)代码中引用了自由变量

接下来就来讲讲实践上的闭包

### 分析
让我们先写个例子，例子依然是来自《JavaScript权威指南》，稍微做点改动：
```js
var scope = "global scope";
function checkscope() {
    var scope = "local scope";
    function f() {
        return scope;
    }
    return f;
}
var foo = checkscope();
console.log(foo());//local scope
```
首先我们要分析一下这段代码中**执行上下文栈**和**执行上下文**的变化情况
另一个与这段代码相似的例子，在《JavaScript深入之执行上下文》中有着非常详细的分析。如果看不懂以下的执行过程，建议先阅读这篇文章。

-----------
这里直接给出简要的执行过程：
>1、进入全局代码，创建全局执行上下文，全局执行上下文被压入执行上下文栈
>2、全局执行上下文初始化
>3、执行checkscope函数，创建checkscope函数执行上下文，checkscope执行上下文被压入执行上下文栈
>4、checkscope执行上下文初始化，创建变量对象、作用域链、this等
>5、checkscope函数执行完毕，checkscope执行上下文从执行上下文栈中弹出
>6、执行f函数，创建f函数执行上下文，f执行上下文被压入执行上下文栈
>7、f执行上下文初始化，创建变量对象、作用域链、this等
>8、f函数执行完毕，f函数上下文从执行上下文栈中弹出

----------
了解到这个过程，我们应该思考一个问题，那就是：
当f函数执行的时候，checkscope函数上下文已经被销毁了(即从执行上下文栈中被弹出),怎么还会读取到 checkscope 作用域下的 scope 值呢？
以上的代码，要是转换成 PHP，就会报错，因为在 PHP 中，f 函数只能读取到自己作用域和全局作用域里的值，所以读不到 checkscope 下的 scope 值。
然而 JavaScript 却是可以的！
当我们了解了具体的执行过程后，我们知道 f 执行上下文维护了一个作用域链：
```js
fContext = {
    Scope: [AO, checkscopeContext.AO, globalContext.VO]
}
```
对的，就是因为这个作用域链，f 函数依然可以读取到 checkscopeContext.AO 的值.说明当 f 函数引用了 checkscopeContext.AO 中的值的时候，即使 checkscopeContext 被销毁了，但是 JavaScript 依然会让 checkscopeContext.AO 活在内存中，f 函数依然可以通过 f 函数的作用域链找到它，正是因为 JavaScript 做到了这一点，从而实现了闭包这个概念。

所以，让我们再看一遍实践角度上闭包的定义：
1、即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）
2、在代码中引用了自由变量

### 接下来，看这道刷题必刷，面试必考的闭包题：
```js
var data = [];
for (var i = 0; i < 3; i++) {
    data[i] = function () {
        console.log(i);
    }
}
data[0]();//3
data[1]();//3
data[2]();//3
```
答案是都是 3，让我们分析一下原因：
当执行到 data[0] 函数之前，此时全局上下文的 VO 为：
```js
globalContext = {
    VO: {
        data: [...],
        i: 3
    }
}
```
当执行 data[0] 函数的时候，data[0] 函数的作用域链为：
```js
data[0]Context = {
    Scope: [AO, globalContext.VO]
}
```
data[0]Context的AO并没有i值，所以会从globalContext.VO中查找,i为3,所以打印的结果就是3
data[1]和data[2]是一样对道理

**所以让我们改成闭包看看：**
```js
var data = [];
for (var i = 0; i < 3; i++) {
    data[i] = (function (i) {
        return function () {
            console.log(i);
        }
    })(i);
}
data[0]();//0
data[1]();//1
data[2]();//2
```
当执行到data[0]函数之前，此时全局上下文的VO为：
```js
globalContext = {
    VO: {
        data: [...],
        i: 3
    }
}
```
跟没改之前一模一样
当执行data[0]函数的时候，data[0]函数的作用域发生了改变:
```js
data[0]Context = {
    Scope: [AO, 匿名函数Context.AO, globalContext.VO],
}
```
**匿名函数执行上下文的AO为：**
```js
匿名函数Context.AO = {
    AO: {
        arguments: {
            0: 0,
            length: 1
        },
        i: 0
    }
}
```
data[0]Context的AO并没有i值，所以会沿着作用域链从匿名函数Context.AO中查找，这时候就会找i为0,找到了就不会往globalContext.VO中查找了，即使globalCOntext.VO也有i的值(值为3),所以打印的结果就是0.
data[1]和data[2]也是一样

>上一篇文章 [深入执行上下文](/All/js/deepStudy/seven "深入执行上下文")
>下一篇文章 [参数按值传递](/All/js/deepStudy/nine "参数按值传递")