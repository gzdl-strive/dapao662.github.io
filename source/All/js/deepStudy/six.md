---
title: 从ECMAScript规范解读this
date: 2021-03-12 15:26:20
tag: js
toc: true
---

### 前言
在《JavaScript深入之执行上下文栈》中讲到，当JavaScript代码执行一段可执行代码(executable code)时，会创建对应的执行上下文(execution context)。
(可执行代码有三种：全局代码、函数代码、eval代码)=>(三种执行上下文：全局执行上下文、函数执行上下文、eval执行上下文)
>对于每个执行上下文，都有三个重要属性:
* 变量对象(Variable object，VO)
* 作用域链(Scope chain)
* this

今天重点讲讲this
……
我们要从 ECMASciript5 规范开始讲起
先奉上 ECMAScript 5.1 规范地址：
[中文版:http://yanhaijing.com/es5/#115](http://yanhaijing.com/es5/#115)
让我们开始了解规范吧！

### Types
ECMAScript 的类型分为**语言类型**和**规范类型**。
ECMAScript 语言类型是开发者直接使用 ECMAScript 可以操作的。其实就是我们常说的Undefined, Null, Boolean, String, Number, 和 Object。
而规范类型相当于 meta-values，是用来用算法描述 ECMAScript 语言结构和 ECMAScript 语言类型的。
规范类型包括：Reference, List, Completion, Property Descriptor, Property Identifier, Lexical Environment, 和 Environment Record。
没懂？没关系，我们只要知道在 ECMAScript 规范中还有一种只存在于规范中的类型，它们的作用是用来描述语言底层行为逻辑。
今天我们要讲的重点是便是其中的 Reference 类型。它与 this 的指向有着密切的关联。

### Reference
那什么又是 Reference ？
>Reference 类型就是用来解释诸如 delete、typeof 以及赋值等操作行为的。

抄袭尤雨溪大大的话，就是：
>这里的 Reference 是一个 Specification Type，也就是 “只存在于规范里的抽象类型”。它们是为了更好地描述语言的底层行为逻辑才存在的，但并不存在于实际的 js 代码中。

再看接下来的这段具体介绍 Reference 的内容：
这段讲述了 Reference 的构成，由三个组成部分，分别是：
* base value
* referenced name
* strict reference
------
可是这些到底是什么呢？
我们简单的理解的话：
`base value` 就是**属性所在的对象**或者**就是 EnvironmentRecord**，它的值只可能是 undefined, an Object, a Boolean, a String, a Number, or an environment record 其中的一种。
`referenced name` 就是**属性的名称**

----------

举个例子：
```js
var foo = 1;
//对应的Reference是：
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
}
```
再举个例子：
```js
var foo = {
    bar: function() {
        return this
    }
};
foo.bar(); //foo
//bar对应的Reference是：
var BarReference = {
    base: foo,
    propertyName: 'bar',
    strict: false
}
```
而且规范中还提供了获取 Reference 组成部分的方法，比如 `GetBase` 和 `IsPropertyReference`。
>1、GetBase:返回reference的base value
>2、IsPropertyReference:如果base value是一个对象，就返回true

---------

**GetValue**
>一个用于从 Reference 类型获取对应值的方法

简单模拟GetValue的使用
```js
var foo = 1;
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
};
GetValue(fooReference)//1
```
GetValue 返回对象属性真正的值，但是要注意：
**调用 GetValue，返回的将是具体的值，而不再是一个 Reference**
这个很重要，这个很重要，这个很重要

### 如何确定this的值
关于 Reference 讲了那么多，为什么要讲 Reference 呢？到底 Reference 跟本文的主题 this 有哪些关联呢？如果你能耐心看完之前的内容，以下开始进入高能阶段：
**这里讲了当函数调用的时候，如何确定 this 的取值。**
>1.计算 MemberExpression 的结果赋值给 ref
>2.判断 ref 是不是一个 Reference 类型
>>2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)
>>2.2 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)
>>2.3 如果 ref 不是 Reference，那么 this 的值为 undefined

### 具体分析
让我们一步一步看：
**1、计算 MemberExpression 的结果赋值给 ref**
什么是 MemberExpression？
举个例子：
```js
function foo() {
    console.log(this);
}
foo();//MemberExpression是foo

function foo() {
    return function () {
        console.log(this);
    }
}
foo()();//MemberExpression是foo()

var foo = {
    bar: function () {
        return this;
    }
}
foo.bar(); //MemberExpression是foo.bar
```
>所以简单理解**MemberExpression其实就是()左边的部分**

**2、判断 ref 是不是一个 Reference 类型**
关键就在于看规范是如何处理各种 MemberExpression，返回的结果是不是一个Reference类型。
举最后一个例子：
```js
var value = 1;
var foo = {
    value: 2,
    bar: function () {
        return this.value;
    }
}
//示例1
console.log(foo.bar());
//示例2
console.log((foo.bar)())
//示例3
console.log((foo.bar = foo.bar)());
//示例4
console.log((false || foo.bar)());
//示例5
console.log((foo.bar, foo.bar)());
```

(1)foo.bar()
在示例1中,MemberExpression计算的结果是foo.bar,那么foo.bar是不是一个Reference呢？
查看规范 11.2.1 Property Accessors，这里展示了一个计算的过程，什么都不管了，就看最后一步：
```js
Return a value of type Reference whose base value is baseValue and whose referenced name is propertyNameString, and whose strict mode flag is strict.
```
我们得知该表达式返回了一个 Reference 类型！
根据之前的内容，我们知道该值为：
```js
var Reference = {
    base: foo,
    name: 'bar',
    strict: false
}
```
接下来按照 2.1 的判断流程走：
2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)
该值是 Reference 类型，那么 IsPropertyReference(ref) 的结果是多少呢？
前面我们已经铺垫了 IsPropertyReference 方法，如果 base value 是一个对象，结果返回 true。
base value为foo,foo是一个对象,所以IsPropertyReference(ref)结果为true
这个时候我们就可以确定this的值了：`this = GetBase(ref)`
GetBase也已经提过，获得base value值,这个例子中就是foo，所以this的值就是foo,示例1的结果就是2

(2)(foo.bar)()
foo.bar 被 () 包住
直接看结果部分：
```js
Return the result of evaluating Expression. This may be of type Reference.
NOTE This algorithm does not apply GetValue to the result of evaluating Expression.
```
实际上 () 并没有对 MemberExpression 进行计算，所以其实跟示例 1 的结果是一样的。

(3)(foo.bar = foo.bar)()
看示例3，有赋值操作符，查看规范 11.13.1 Simple Assignment ( = ):
计算的第三步：
```js
3.Let rval be GetValue(rref).
```
因为使用了` GetValue`，所以返回的值不是 Reference 类型，
按照之前讲的判断逻辑：
2.3 如果 ref 不是Reference，那么 this 的值为 undefined
this 为 undefined，非严格模式下，this 的值为 undefined 的时候，其值会被隐式转换为全局对象。

(4)(false || foo.bar)()
看示例4，逻辑与算法，查看规范 11.11 Binary Logical Operators：
计算第二步：
```js
2.Let lval be GetValue(lref).
```
因为使用了 GetValue，所以返回的不是 Reference 类型，this 为 undefined

(5)(foo.bar, foo.bar)()
看示例5，逗号操作符，查看规范11.14 Comma Operator ( , )
计算第二步：
```js
2.Call GetValue(lref).
```
因为使用了 GetValue，所以返回的不是 Reference 类型，this 为 undefined

### 揭晓结果
所以最后一个例子的结果是：
```js
var value = 1;
var foo = {
  value: 2,
  bar: function () {
    return this.value;
  }
}
//示例1
console.log(foo.bar()); // 2
//示例2
console.log((foo.bar)()); // 2
//示例3
console.log((foo.bar = foo.bar)()); // 1
//示例4
console.log((false || foo.bar)()); // 1
//示例5
console.log((foo.bar, foo.bar)()); // 1
```
>注意：以上是在非严格模式下的结果，严格模式下因为 this 返回 undefined，所以示例 3 会报错。

### 补充
最最后，忘记了一个最最普通的情况：
```js
function foo() {
    console.log(this);
}
foo();
```
MemberExpression 是 foo，解析标识符，查看规范 10.3.1 Identifier Resolution，会返回一个 Reference 类型的值：
```js
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
}
```
接下来进行判断：
2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)
因为 base value 是 EnvironmentRecord，并不是一个 Object 类型，还记得前面讲过的 base value 的取值可能吗？ 只可能是 undefined, an Object, a Boolean, a String, a Number, 和 an environment record 中的一种。
IsPropertyReference(ref) 的结果为 false，进入下个判断：
2.2 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)
base value 正是 Environment Record，所以会调用 ImplicitThisValue(ref)
查看规范 10.2.1.1.6，ImplicitThisValue 方法的介绍：该函数始终返回 undefined。
所以最后 this 的值就是 undefined。

### 多说一句
尽管我们可以简单的理解this为调用函数的对象，如果是这样的话，如何解释下面这个例子呢？
```js
var value = 1;
var foo = {
  value: 2,
  bar: function () {
    return this.value;
  }
}
console.log((false || foo.bar)()); // 1
```
此外，又如何确定调用函数的对象是谁呢？在写文章之初，我就面临着这些问题，最后还是放弃从多个情形下给大家讲解 this 指向的思路，而是追根溯源的从 ECMASciript 规范讲解 this 的指向，尽管从这个角度写起来和读起来都比较吃力，但是一旦多读几遍，明白原理，绝对会给你一个全新的视角看待 this 。而你也就能明白，尽管 foo() 和 (foo.bar = foo.bar)() 最后结果都指向了 undefined，但是两者从规范的角度上却有着本质的区别。

>上一篇文章 [作用域链](/All/js/deepStudy/five "作用域链")
>下一篇文章 [深入执行上下文](/All/js/deepStudy/seven "深入执行上下文")