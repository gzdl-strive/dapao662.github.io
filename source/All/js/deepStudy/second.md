---
title: 词法作用域和动态作用域
date: 2021-03-11 16:34:20
tag: js
toc: true
---

### 作用域
>作用域是指程序源代码中定义变量的区域
>作用域规定了如何查找变量，也就是确定当前执行代码对变量的访问权限。
>JavaScript 采用词法作用域(lexical scoping)，也就是静态作用域

### 静态作用域和动态作用域
>因为 JavaScript 采用的是词法作用域，函数的作用域在函数定义的时候就决定了。
>而与词法作用域相对的是动态作用域，函数的作用域是在函数调用的时候才决定的。

**看如下代码**
```js
let value = 1;
function foo () {
    console.log(value);
}
function bar() {
    let value = 2;
    foo();
}
bar();//1
```
**假设JavaScript采用静态作用域，让我们分析下执行过程：**
>执行 foo 函数，先从 foo 函数内部查找是否有局部变量 value，如果没有，就根据书写的位置，查找上面一层的代码，也就是 value 等于 1，所以结果会打印 1。

**假设JavaScript采用动态作用域，让我们分析下执行过程：**
>执行 foo 函数，依然是从 foo 函数内部查找是否有局部变量 value。如果没有，就从调用函数的作用域，也就是 bar 函数内部查找 value 变量，所以结果会打印 2。

前面我们已经说了，JavaScript采用的是静态作用域，所以这个例子的结果是 1。

### 动态作用域
>也许你会好奇什么语言是动态作用域？bash就是动态作用域

### 思考题，让我们看看js权威指南中的例子
```js
let scope = "global scope";
function checkscope() {
    var scope = "local scope";
    function f() {
        return scope;
    }
    return f();
}
function checkscope2() {
    var scope = "local scope2";
    function f() {
        return scope;
    }
    return f;
}
const str1 = checkscope();
const str2 = checkscope2()();
console.log(str1);//local scope
console.log(str2);//local scope2
```
**原因也很简单，因为JavaScript采用的是词法作用域，函数的作用域基于函数创建的位置。**

而引用《JavaScript权威指南》的回答就是：
>JavaScript 函数的执行用到了作用域链，这个作用域链是在函数定义的时候创建的。嵌套的函数 f() 定义在这个作用域链里，其中的变量 scope 一定是局部变量，不管何时何地执行函数 f()，这种绑定在执行 f() 时依然有效。

但是在这里真正想让大家思考的是：
虽然两段代码执行的结果一样，但是两段代码究竟有哪些不同呢？
如果要回答这个问题，就要牵涉到很多的内容，词法作用域只是其中的一小部分，让我们期待下一篇文章————《JavaScript深入之执行上下文栈》

>上一篇文章 [JS原型链](/All/js/deepStudy/first "JS原型链")
>下一篇文章 [执行上下文栈](/All/js/deepStudy/third "执行上下文栈")