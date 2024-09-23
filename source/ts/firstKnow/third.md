---
title: 函数返回值
date: 2020-12-28 09:49:02
tag: ts
---

## 没有返回值函数
```ts
function hello() :void {
    alert('hello');
}
```

## 有返回值函数
```ts
function show(name: string, age: number):string {
    return `名字:${name}，年龄:${age}`;
}
```

## 错误示例
```ts
function show(name: string, age: number):void {
    console.log(`名字:${name}，年龄:${age}`);//正确
    //return 12;错误;void没有返回值
}
show('huang', 20);
```