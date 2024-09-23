---
title: ES6-箭头函数、rest、扩展运算符
toc: true
date: 2021-11-15 17:28:02
---

## 箭头函数

ES6 允许使用「箭头」（=>）定义函数。

```js
/**
 * 1. 通用写法
 */
let fn = (arg1, arg2, arg3) => {
  return arg1 + arg2 + arg3;
};
```

箭头函数的注意点:
(1) 如果形参只有一个，则小括号可以省略
(2) 函数体如果只有一条语句，则花括号可以省略，函数的返回值为该条语句的
执行结果
(3) 箭头函数 this 指向声明时所在作用域下 this 的值
(4) 箭头函数不能作为构造函数实例化
(5) 不能使用 arguments

```js
/**
* 2. 省略小括号的情况
*/
let fn2 = num => {
 return num * 10;
};

/**
* 3. 省略花括号的情况
*/
let fn3 = score => score * 20;

/**
* 4. this 指向声明时所在作用域中 this 的值
*/
let fn4 = () => {
  console.log(this);
}
let school = {
  name: '尚硅谷',
  getName(){
    let fn5 = () => {
      console.log(this);
    }
    fn5();
  }
```

<span style="color: red">注意：箭头函数不会更改 this 指向，用来指定回调函数会非常合适</span>

## rest 参数

ES6 引入 rest 参数，用于获取函数的实参，用来代替 arguments

```js
/**
 * 作用与 arguments 类似
 */
function add(...args) {
  console.log(args);
}
add(1, 2, 3, 4, 5);
/**
 * rest 参数必须是最后一个形参
 */
function minus(a, b, ...args) {
  console.log(a, b, args);
}
minus(100, 1, 2, 3, 4, 5, 19);
```

<span style="color: red">注意：rest 参数非常适合不定个数参数函数的场景</span>

## spread 扩展运算符

扩展运算符（spread）也是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列，对数组进行解包。

```js
/**
 * 展开数组
 */
let tfboys = ["德玛西亚之力", "德玛西亚之翼", "德玛西亚皇子"];
function fn() {
  console.log(arguments);
}
fn(...tfboys);

/**
 * 展开对象
 */
let skillOne = {
  q: "致命打击",
};
let skillTwo = {
  w: "勇气",
};
let skillThree = {
  e: "审判",
};
let skillFour = {
  r: "德玛西亚正义",
};
let gailun = { ...skillOne, ...skillTwo, ...skillThree, ...skillFour };
```
