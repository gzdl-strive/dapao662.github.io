---
title: ES6-迭代器、生成器、Promise
date: 2021-11-16 09:33:02
toc: true
---

## 迭代器

遍历器(iterator)就是一种机制。它是一种接口，为各种不同的数据解构提供统一的访问机制。任何数据解构只要部署 `iterator` 接口，就可以完成遍历操作。
(1) ES6 创造了一种新的遍历命令 for...of 循环，Iterator 接口主要供 for...of 消费
(2) 原生具备 iterator 接口的数据(可用 for of 遍历)
(a) Array
(b) Arguments
(c) Set
(d) Map
(e) String
(f) TypedArray
(g) NodeList

**工作原理**
(a) 创建一个指针对象，指向当前数据结构的起始位置
(b) 第一次调用对象的 next 方法，指针自动指向数据结构的第一个成员
(c) 接下来不断调用 next 方法，指针一直往后移动，直到指向最后一个成员
(d) 每调用 next 方法返回一个包含 value 和 done 属性的对象

**迭代器例子**

```js
//声明一个数组
const xiyou = ["唐僧", "孙悟空", "猪八戒", "沙僧"];

//使用 for...of 遍历数组
for (let v of xiyou) {
  console.log(v);
}

let iterator = xiyou[Symbol.iterator]();
//调用对象的next方法
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

**迭代器自定义遍历对象**

```js
//声明一个对象
const banji = {
  name: "终极一班",
  stus: ["xiaoming", "xiaoning", "xiaotian", "knight"],
  [Symbol.iterator]() {
    //索引变量
    let index = 0;
    //保存this指向
    let _this = this;
    return {
      next: function () {
        if (index < _this.stus.length) {
          const result = { value: _this.stus[index], done: false };
          //下标自增
          index++;
          //返回结果
          return result;
        } else {
          return { value: undefined, done: true };
        }
      },
    };
  },
};

//遍历这个对象
for (let v of banji) {
  console.log(v);
}
```

<span style="color: red">注: 需要自定义遍历数据的时候，要想到迭代器。</span>


## 生成器
生成器函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同

```js
function * gen(){
 yield '一只没有耳朵';
 yield '一只没有尾巴';
 return '真奇怪';
}
let iterator = gen();
console.log(iterator.next());
console.log(iterator.next());
console.log(iterator.next());
```

代码说明：
(1) * 的位置没有限制
(2) 生成器函数返回的结果是迭代器对象，调用迭代器对象的 next 方法可以得到yield 语句后的值
(3) yield 相当于函数的暂停标记，也可以认为是函数的分隔符，每调用一次 next方法，执行一段代码
(4) next 方法可以传递实参，作为 yield 语句的返回值

## Promise
Promise 是 ES6 引入的异步编程的新解决方案。语法上 Promise 是一个构造函数，
用来封装异步操作并可以获取其成功或失败的结果。
(1) Promise 构造函数: Promise (excutor) {}
(2) Promise.prototype.then 方法
(3) Promise.prototype.catch 方法

**具体解析**
>[Promise解析及其实现](/All/js/promise "Promise")
