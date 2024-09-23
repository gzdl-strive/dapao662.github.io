---
title: ES6的TDZ(暂时性死区)
date: 2020-10-30 10:44:36
tags: js
---

### 什么是TDZ？
>`Temporal Dead Zone(TDZ)`是 **ES6(ES2015)** 中对新作用域的专业语义。

### let/const和var的区别
在`ES6`之前，JS的`scope`只有两种，**全局作用域**和**函数作用域**，但是在ES6中出现了**块级作用域**，即使用`let/const`可以定义块级作用域。
那么在ES6的新特性中，最容易看到TDZ作用的就是使用let/const的使用上面。
`let/const`与`var`的主要不同有两个地方:
* `let/const`是使用区块作用域；`var`是使用函数作用域
* 在`let/const`声明之前就访问对应的变量与常量，会抛出`ReferenceError`错误；但在`var`声明之前就访问对应的变量，则会得到`undefined`.
```js
console.log(name1); // undefined;
console.log(name2); // ReferenceError
var name1 = 'name1';
let name2 = 'name2';
```
实践证明当我们在未声明之前使用`var`定义的变量时会得到`undefined`,但是在使用`let`未定义的变量时会抛出错误。因为ES6中的**let声明的变量是不存在变量提升的作用**。
```js
var x = 10;
if (true) {
    x = 20; // ReferenceError
    let x;
 }
```
ES6 明确规定，如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了**封闭作用域**。凡是在声明之前就使用这些变量，就会报错。
总之，在代码块内，使用`let`命令*声明变量之前*，*该变量都是不可用的*。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。
```js
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
上面代码中，在`let`命令声明变量`tmp`之前，都属于变量`tmp`的**死区**。

### const的认识
其实`const`的行为和`let`的行为差不多。只是`const`定义的是常量，不变的数据。`let`定义的是改变的数据。并且在声明`const`的时候必须存在值。
```js
const name; //error
const name = 'my name' 
name = 'your name' // error
let age;
age = 18;
```
**对于const 需要明确一点，就是改变的是什么？看下面这个**
```js
const person = {
    name: "Nicholas"
};

// works
person.name = "Greg";

// throws an error
person = {
    name: "Greg"
};
```
可以更改`person.name`而不会导致错误，因为这会更改`person`包含的内容，并且不会更改`person`绑定的值。当此代码尝试为`person`分配值**从而尝试更改绑定**时，将引发错误。`const`如何与对象一起工作的这种微妙之处很容易被误解。 请记住：**const阻止修改绑定，而不是阻止修改绑定值。**

### typeof的“死区”陷阱
我们都知道使用`typeof `可以用来检测给定变量的数据类型，也可以使用它来判断值是否被定义。当返回`undefined`时表示值未定义;
但是在`const/let`定义的变量在变量声明之前如果使用了`typeof`就会报错
```js
typeof x; // ReferenceError
let x;
```
因为`x`是使用`let`声明的，那么在**x未声明之前都属于暂时性死区**，在使用`typeof`时就会报错。所以在使用`let/const`进行声明的变量在使用`typeof`时不一定安全。
```js
typeof y; // 'undefined'
```
但是可以看出，如果我们只是用了`typeof`而没有去定义，也是不会报错的，从这例可以看出只要没有明确规定使用`const/let`定义之前就是不会出错。

### 传参的“死区”陷阱
例如下面一段代码，我们在使用
```js
function bar (x = y, y = 2) {
    return [x, y];
}

bar(); //报错
```
上面代码中，调用`bar`函数之所以报错（某些实现可能不报错），是因为参数x默认值等于另一个参数y，而此时y还没有声明，属于”死区“。如果y的默认值是x，就不会报错，因为此时x已经声明了。
```js
function bar(x = 2, y = x) {
  return [x, y];
}
bar(); // [2, 2]
```
使用var和let声明的另外一种区别:
```js
// 不报错
var x = x;

// 报错
let x = x;
// ReferenceError: x is not defined
```
受“死区”的影响，使用`let`声明变量时，只要变量在还没有声明完成前使用，就会报错。上面这行就属于这个情况，在变量x的声明语句还没有执行完成前，就去取x的值，导致报错”x 未定义“。

## 总结
`ES6 `规定暂时性死区和`let、const`语句不出现变量提升，**主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为**。这样的错误在` ES5 `是很常见的，现在有了这种规定，避免此类错误就很容易了。
>总之，`暂时性死区的本质`就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

    注: TDZ最一开始是为了const所设计的，但后来的对let的设计也是一致的。
    注: 在ES6标准中，对于const所声明的识别子仍然也经常为variable(变量)，称为constant variable(固定的变量)。以const声明所创建出来的常量，在JS中只是不能再被赋(can’t re-assignment)，并不是不可被改变(immutable)的，这两种概念仍然有很大的差异。