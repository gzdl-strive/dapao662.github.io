---
title: 柯里化函数
date: 2021-06-09 14:37:24
toc: true
---

## 函数柯里化概念
>在计算机科学中，柯里化(curring)是把接受多个参数的函数变换成接受一个单一参数(最初函数的第一个参数)的函数，并且返回接受剩余的参数且返回结果的新函数的技术。
>通俗点说就是将一个函数拆分成多个函数，是固定部分参数，返回一个接受剩余参数的函数，也称为部分计算函数，目的是为了减小适应范围，场景一个针对性更强的函数。

**简单例子**
```js
//实现一个简单的加法
function add(a, b) {
  return a + b;
}
add(1, 2);

//用柯里化实现
function curryAdd(a) {
  return function (b) {
    return a + b;
  }
}
console.log(curryAdd(1)(2));//3
```
上面的例子就是使用柯里化将add函数拆分成了两个函数，currying首次执行返回一个新的函数，然后再次调用返回结构，返回一个函数的特征就是高阶函数，柯里化函数就是高级函数的一种。

### add(1)(2,3)(4)=10
```js
//柯里化add函数
function add() {
  let args = [...arguments];
  let fn = function () {
    args.push(...arguments);
    return fn;
  }
  //隐式转换
  fn.toString = function () {
    return args.reduce((pre, cur) => {
      return pre + cur;
    })
  }
  return fn;
}
console.log(add(1)(2,3)(4));//10
```

以上就是函数柯里化的简单实现。继续来实现一些例子来看看柯里化的好处
### 判断数据类型
```js
//实现一个判断数据类型的方法
const checkType = function (type, content) {
  //借用Object.prototype.toString方法
  return Object.prototype.toString.call(content) === `[object ${type}]`;
}
checkType('Number', 2);//true
```
这种方法总是要把type参数传递过去，如果写错了就会影响正确的结果，可以考虑下如何做到把"Number"做到复用
```js
//柯里化
const curry = function (type) {
  return function(content) {
    return Object.prototype.toString.call(content) === `[object ${type}]`;
  }
}
const isNumber = curry('Number');
isNumber(3);//true
```
函数柯里化是可以给我们带来一下便捷，但是也是会有缺点的，在性能上也会受到影响，比如add函数里面需要创建数组去存放每次调用的时候的参数，创建闭包函数这些都会对内存跟速度上会带来花销，存取arguments对象通常要比存取命名参数要慢一点。

## 柯里化函数
>概念：把接受多个参数的函数变换成接受单一参数的函数，并且返回接受剩余参数且返回结果的新函数的技术

**使用场景**
>* 参数复用
>* 提前确认，避免每次都重复判断
>* 延迟计算/运行

```js
function add(a, b, c) {
  return a + b + c;
}
```
**目标：把add函数柯里化**
```js
//柯里化函数
/**
* @params {Function} fn
*/
function curry(fn) {
  //记录fn函数的参数个数
  const len = fn.length;
  return function demo() {
    //收集参数
    let args = [...arguments];
    if (args.length >= len) {
      //当收集到的参数个数大于或等于fn函数的参数个数时，执行fn函数并把收集到的参数传入然后返回执行结果
      return fn(...args);
    } else {
      //当收集到的参数个数小于fn函数时，不触发fn函数，返回demo函数，继续收集参数
      return function () {
        return demo(...args, ...arguments);
      }
    }
  }
}
let curryAdd = curry(add);
let s1 = curryAdd(5);
console.log('5+: ', s1(2, 3));// 5+: 10
console.log('5+: ', s1(1)(4));// 5+: 10
```

### 应用场景：提前确认，避免每次都重复判断
```js
//判断浏览器是否支持事件监听函数addEventListener
let addEvent = function(el, type, fn, capture) {
  if (window.addEventListener) {
    //addEventListener函数接收三个参数：分别是 事件名称、监听函数、是否捕获(默认为false,只在冒泡阶段触发)
    el.addEventListener(type, function (e) {
      //使用call方法，解决this指向问题，使得this指向el这个DOM节点
      fn.call(el, e);
    }, capture);
  } else if (window.attachEvent) {
    //兼容IE等浏览器使用该方法，接收两个参数：on+事件名称、监听函数
    el.attachEvent('on' + type, function (e) {
      fn.call(el, e);
    });
  }
}
```
上面的代码，每次使用addEvent为元素添加事件的是否，(eg.IE6/7)都会走一遍if...else if ...,其实只要一次判断就可以了，使用函数柯里化进行优化：
```js
let addEvent = (function() {
  if (window.addEventListener) {
    return function(el, type, listener, capture) {
      el.addEventListener(type, function(e) {
        listener.call(el, e);
      }, capture)
    }
  } else if (window.attachEvent) {
    return function(el, type, listener, capture) {
      el.attachEvent('on' + type, function(e) {
        listener.call(el, e);
      });
    }
  }
})();
```