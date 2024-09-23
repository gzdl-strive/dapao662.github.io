---
title: ES6-块级作用域(let/const关键字)
toc: true
date: 2021-11-15 17:01:02
---

## let 关键字

`let`关键字用来声明变量，使用 let 声明的变量有几个特点：
(1) 不允许重复声明
(2) 块儿级作用域
(3) 不存在变量提升
(4) 不影响作用域链
<span style="color: red">应用场景：以后声明变量使用 let 就对了</span>

**let 变量特点**

```js
//声明变量
let a;
let b, c, d;
let e = 100;
let f = 521,
  g = "iloveyou",
  h = [];

//1. 变量不能重复声明
let star = "罗志祥";
let star = "小猪";

//2. 块儿级作用域  全局, 函数, eval
// if else while for
{
  let girl = "周扬青";
}
console.log(girl);
//girl is not define

//3. 不存在变量提升
console.log(song);
let song = "恋爱达人";
//Cannot access 'song' before initialization(不能在初始化song之前访问)

//4. 不影响作用域链
{
  let school = "尚硅谷";
  function fn() {
    console.log(school);
  }
  fn();
}
```

## const 关键字

`const`关键字用来声明常量，const 声明有以下特点
(1) 声明必须赋初始值
(2) 标识符一般为大写
(3) 不允许重复声明
(4) 值不允许修改
(5) 块儿级作用域
<span style="color: red">注意: 对象属性修改和数组元素变化不会出发 const 错误</span>
<span style="color: red">应用场景：声明对象类型使用 const，非对象类型声明选择 let</span>

**const 特点**

```js
//声明常量
const NAME = "S1mple";

//1. 一定要赋初始值
const A;

//2. 一般常量使用大写(潜规则)
const a = 100;

//3. 常量的值不能修改
NAME = 'ELECTRONIC';
//4. 块儿级作用域
{
    const PLAYER = 'UZI';
}
console.log(PLAYER);

//5. 对于数组和对象的元素修改, 不算做对常量的修改, 不会报错
const TEAM = ["UZI", "MXLG", "Ming", "Letme"];
TEAM.push('Meiko');
```
