---
title: ES6-变量的解构赋值、模板字符串、简化对象写法
toc: true
date: 2021-11-15 17:14:02
---

## 1、变量的解构赋值

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为**解构赋值**。

**数组的解构赋值**

```js
const F4 = ["小沈阳", "刘能", "赵四", "宋小宝"];
let [xiao, liu, zhao, song] = F4;
console.log(xiao);
console.log(liu);
console.log(zhao);
console.log(song);
```

**对象的解构赋值**

```js
const zhao = {
  name: "赵本山",
  age: "不详",
  xiaopin: function () {
    console.log("我可以演小品");
  },
};

let { name, age, xiaopin } = zhao;
console.log(name);
console.log(age);
console.log(xiaopin);
xiaopin();
```

**复杂解构**

```js
let wangfei = {
  name: "王菲",
  age: 18,
  songs: ["红豆", "流年", "暧昧", "传奇"],
  history: [{ name: "窦唯" }, { name: "李亚鹏" }, { name: "谢霆锋" }],
};
let {
  songs: [one, two, three],
  history: [first, second, third],
} = wangfei;
```

<span style="color: red">注意：频繁使用对象方法、数组元素，就可以使用解构赋值形式</span>

## 2、模板字符串

模板字符串（`template string`）是增强版的字符串，用反引号标识，特点：
(1) 字符串中可以出现换行符
(2) 可以使用 ${xxx} 形式输出变量

```js
// ES6 引入新的声明字符串的方式 『``』 '' ""
//1. 声明
let str = `我也是一个字符串哦!`;
console.log(str, typeof str);

//2. 内容中可以直接出现换行符
let str = `<ul>
  <li>沈腾</li>
  <li>玛丽</li>
  <li>魏翔</li>
  <li>艾伦</li>
  </ul>`;

//3. 变量拼接
let lovest = "魏翔";
let out = `${lovest}是我心目中最搞笑的演员!!`;
console.log(out);
```

注意：当遇到字符串与变量拼接的情况使用模板字符串

## 3、简化对象写法

ES6 允许在大括号里面，**直接写入变量和函数，作为对象的属性和方法**。这样的书写更加简洁。

```js
let name = "尚硅谷";
let change = function () {
  console.log("我们可以改变你!!");
};

const school = {
  name,
  change,
  improve() {
    console.log("我们可以提高你的技能");
  },
};

console.log(school);
```
