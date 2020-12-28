---
title: 变量、参数、返回值
date: 2020-12-28 09:49:02
tag: ts
---

## 变量
```ts
let a: number = 12;
let a: boolean = false;
let a: string = 'apple';
let a: any = 12;//任意类型
```

## 参数
```ts
function show(a:number, b:number) {
    return a + b;
}
console.log(show(12,13));//25
```

## 返回值
```ts
function show2(a:number, b:number):number {
    return a + b;
}
console.log(show2(12,12));//24
```