---
title: 数组、元组、枚举
date: 2020-12-28 09:55:02
tag: ts
---

## 数组
```ts
let a: number[] = [1, 2, 'abc'];//错误
let a: (number|string)[] = [1, 2, 'abc'];//正确
a.push(true);//错误
```

## 元组
```ts
let a:[number, boolean] = [12, true];
a.push(33);//成功，只管前两种
console.log(a);//[ 12, true, 33 ]
```

## 枚举
```ts
enum Color {Red, Green, Blue};
let c: Color = Color.Blue;
console.log(c);//2
```