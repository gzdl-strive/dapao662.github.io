---
title: 模块化的理解
date: 2021-04-04 14:20:20
tag: js
toc: true
---

### 1、什么是模块?
>1. 将一个复杂的程序依据一定的规则(规范)封装成几个块(文件),并进行组合一起
>2. 块的内部数据与实现是私有的，只是向外暴露一些接口(方法)与外部其他模块通信

### 2、模块化的进化过程
**1.全局function模式：将不同的功能封装成不同的全局函数**
(1)编码：将不同的功能封装成不同的全局函数
(2)问题：污染全局命名空间，容易引起命名冲突或数据不安全，而且模块成员之间看不出直接关系
```js
function m1() {
    //...
}
function m2() {
    //...
}
```

**2.namespace模式：简单对象封装**
(1)作用：减少了全局变量，解决命名冲突
(2)问题：数据不安全(外部可以直接修改模块内部的数据)
```js
let myModule = {
    data: 'simple shotdown',
    foo() {
        console.log(`foo(): ${this.data}`);
    },
    bar() {
        console.log(`bar()：${this.data}`);
    }
}
myModule.foo();//foo(): simple shotdown
myModule.data = 'electronic rush';
myModule.foo();//foo(): electronic rush
```
这样的写法会暴露所有模块成员，内部状态可以被外部改写。

**3. IIFE模式：匿名函数自调用(闭包)**
(1)作用：数据是私有的，外部只能通过暴露的方法操作
(2)编码：将数据和行为封装到一个函数内部，通过给window添加属性来向外暴露接口
(3)问题：如果当前这个模块依赖另一个模块怎么办？
```js
// index.html文件
<script type="text/javascript" src="module.js"></script>
<script type="text/javascript">
    myModule.foo()
    myModule.bar()
    console.log(myModule.data) //undefined 不能访问模块内部数据
    myModule.data = 'xxxx' //不是修改的模块内部的data
    myModule.foo() //没有改变
</script>
// module.js文件
(function(window) {
let data = 'www.baidu.com'
//操作数据的函数
function foo() {
    //用于暴露有函数
    console.log(`foo() ${data}`)
}
function bar() {
    //用于暴露有函数
    console.log(`bar() ${data}`)
    otherFun() //内部调用
}
function otherFun() {
    //内部私有的函数
    console.log('otherFun()')
}
//暴露行为
window.myModule = { foo, bar } //ES6写法
})(window)
```
最后得到的结果：
```js
foo() www.baidu.com
bar() www.baidu.com
otherFun()
undefined
foo() www.baidu.com
```
**4. IIFE模式增强：引入依赖**
这就是现代模块实现的基石
```js
// module.js文件
(function(window, $) {
let data = 'www.baidu.com'
//操作数据的函数
function foo() {
    //用于暴露有函数
    console.log(`foo() ${data}`)
    $('body').css('background', 'red')
}
function bar() {
    //用于暴露有函数
    console.log(`bar() ${data}`)
    otherFun() //内部调用
}
function otherFun() {
    //内部私有的函数
    console.log('otherFun()')
}
//暴露行为
window.myModule = { foo, bar }
})(window, jQuery)
```
```html
    // index.html文件
<!-- 引入的js必须有一定顺序 -->
<script type="text/javascript" src="jquery-1.10.1.js"></script>
<script type="text/javascript" src="module.js"></script>
<script type="text/javascript">
    myModule.foo()
</script>
```
上例子通过jquery方法将页面的背景颜色改成红色，所以必须先引入jQuery库，就把这个库当作参数传入。这样做除了保障模块的独立性，还使得模块之间的依赖关系变得明显

### 3、模块化的好处
>(1)避免命名冲突(减少命名空间污染)
(2)更好的分离，按需加载
(3)更高复用性
(4)高可维护性

### 4. 引入多个`<script>`后出现问题

>(1)请求过多:首先我们要依赖多个模块，那样就会发送多个请求，导致请求过多
>(2)依赖模糊:我们不知道他们的具体依赖关系是什么，也就是说很容易因为不了解他们之间的依赖关系导致加载先后顺序出错。
>(3)难以维护:以上两种原因就导致了很难维护，很可能出现牵一发而动全身的情况导致项目出现严重的问题。模块化固然有多个好处，然而一个页面需要引入多个js文件，就会出现以上这些问题。而这些问题可以通过模块化规范来解决，下面介绍开发中最流行的commomjs,Amd,ES6,CMD规范。

