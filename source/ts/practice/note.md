---
title: ts笔记
date: 2020-12-28 10:50:02
tag: ts
toc: true
---

>TypeScript执行 tsc xxx.ts -> 把ts文件转换成js文件
然后再执行 node xxx.js
 -->简化执行步骤-使用包ts-node
-->ts-node使用ts-node命令执行代码ts-node xxx.ts
ts-node包内部偷偷将ts->js，然后执行js代码

**ts是js的超集,为js添加了类型系统**

**问题**
* 1、JavaScript的两个运行环境? 1、浏览器2、node.js
* 2、TypeScript能直接在浏览器或Node.js中执行吗？不能
* 3、如何将ts编译成js? tsc xxx.ts -> node xxx.js

## 变量
` let age:number = 16;`
### 类型注解:是一种为变量添加类型约束的方式
**约定了什么类型，就只能给变了赋什么类型的值**
**变量命名规则:只能出现数字、字母、下划线、美元符号，并且不能以数字开头**

## TS的类型断言
>调用`querySelector()`通过id选择器获取DOM元素时，拿到的元素类型是`Element`.
因为无法根据id来确定元素的类型，所以，该方法就返回一个宽泛的类型: 元素`(Element)`类型
不管是h1还是img都是元素
导致新问题：无法访问`img元素`的`src属性`了
因为`Element`类型只包含所有元素共有的属性和方法(比如：`id属性`)

**解决方法:使用类型断言，来手动指定更加具体的类型(比如，此处应该比Element类型更加具体)**
**使用场景:当你比TS更了解某个值的类型，并且需要指定更具体的类型时**
* 语法：值 as 更具体的类型
```ts
let img = document.querySelector('#image') as HTMLImageElement;
```
* 解释：我们确定`id="image"`的元素是图片元素，所以，我们将类型指定为`HTMLImageElement`.

## DOM操作

### DOM操作文本内容
>读取：`dom.innerText`
设置: `dom.innerText = 'xxx'`

### DOM操作样式
>`dom.style属性`：行内样式操作，可以设置每一个样式属性（比如，字体大小，文字颜色等）
`dom.classList属性`：类样式操作，也就是操作类名，比如，添加类名，移除类名

**dom.classList属性**
*添加、移除、判断是否存在这三个常用方法*
**添加： dom.classList.add(类名1, 类名2, ...)**
`<p class="a"></p>` -> `dom.classList.add('b', 'c')` -->添加class样式 ==>`class="a b c"`
**移除:dom.classList.remove(类名1, 类名2, ...)**
**判断是否存在: dom.classList.contains(类名)**

## 操作事件
### addEventListenter事件-->给dom元素添加事件
>`dom.addEventListenter`(事件名称，事件处理程序（回调函数）)
事件对象(event),是事件处理程序(回调的参数)
表示与当前事件相关的信息，比如：事件类型(type)、触发事件的DOM元素(target)等。
如果事件只需要触发一次，可以在添加事件处理时处理
处理方式:传入第三个参数,将once属性设置为true
`btn.addEventListenter('click',function() {}, { once: true})`

### removeEventListener移除事件
>`dom.removeEventListener`(事件名称,事件处理程序)
添加和移除的事件处理程序必须是同一个，否则无法移除
`function handleClick() {}; btn.addeventListener('click', hanldeClick); btn.removeEventListener('click', handleClick);`

### 函数声明形式的事件处理程序中，使用事件对象是，应该指定参数类型.
**函数声明在当前ts文件中的任意位置都有定义**
```ts
btn.addEventListener('click', handleClick)
function handleClick(){}
```
**函数声明形式的事件处理程序中，使用事件对象是，应该指定参数类型.**
```ts
btn.addEventListener('click', handleClick);
function handleClick(event: MouseEvent);
```