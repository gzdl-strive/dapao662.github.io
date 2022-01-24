---
title: 数值扩展和对象扩展
date: 2021-11-16 09:59:02
toc: true
---

## 数值扩展

### 1、二进制和八进制

ES6 提供了二进制和八进制数值的新的写法，分别用前缀 0b 和 0o 表示。

```js
//1. 二进制和八进制
let b = 0b1010;
let o = 0o777;
let d = 100;
let x = 0xff;
```

### 2、Number.isFinite()与 Number.isNaN()

`Number.isFinite()` 用来检查一个数值是否为有限的
`Number.isNaN()` 用来检查一个值是否为 NaN

```js
//2. Number.isFinite  检测一个数值是否为有限数
console.log(Number.isFinite(100));
console.log(Number.isFinite(100 / 0));
console.log(Number.isFinite(Infinity));
```

```js
//3. Number.isNaN 检测一个数值是否为 NaN
console.log(Number.isNaN(123));
```

### 3、Number.parseInt() 与 Number.parseFloat()

ES6 将全局方法 parseInt 和 parseFloat，移植到 Number 对象上面，使用不变。

```js
//4. Number.parseInt Number.parseFloat 字符串转整数
console.log(Number.parseInt("5211314love"));
console.log(Number.parseFloat("3.1415926 神奇"));
```

### 4、Math.trunc

用于去除一个数的小数部分，返回整数部分。

```js
//5. Math.trunc 将数字的小数部分抹掉
console.log(Math.trunc(3.5));
```

### 5、Number.isInteger

`Number.isInteger()` 用来判断一个数值是否为整数

```js
//6. Number.isInteger 判断一个数是否为整数
console.log(Number.isInteger(5));
console.log(Number.isInteger(2.5));
```

## 对象扩展

ES6 新增了一些 Object 对象的方法
(1) Object.is 比较两个值是否严格相等，与『===』行为基本一致（+0 与 NaN）
(2) Object.assign 对象的合并，将源对象的所有可枚举属性，复制到目标对象
(3) `__proto__、setPrototypeOf、 setPrototypeOf` 可以直接设置对象的原型

```js
//1. Object.is 判断两个值是否完全相等
console.log(Object.is(120, 120)); // ===  true
console.log(Object.is(NaN, NaN)); // === true
console.log(NaN === NaN); // === false

//2. Object.assign 对象的合并
const config1 = {
  host: "localhost",
  port: 3306,
  name: "root",
  pass: "root",
  test: "test",
};
const config2 = {
  host: "http://atguigu.com",
  port: 33060,
  name: "atguigu.com",
  pass: "iloveyou",
  test2: "test2",
};
console.log(Object.assign(config1, config2));

//3. Object.setPrototypeOf 设置原型对象  Object.getPrototypeof
const school = {
  name: "666",
};
const cities = {
  xiaoqu: ["a", "b", "c"],
};
Object.setPrototypeOf(school, cities);
console.log(Object.getPrototypeOf(school));
console.log(school);
```
