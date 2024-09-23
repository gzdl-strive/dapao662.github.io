---
title: ts笔记
date: 2020-12-28 09:30:02
tag: ts
toc: true
---

* typescript -> 微软

* 比js好在哪里? ->补充js没有的特性——类型、抽象、接口,比js更严谨

## 1、类型
**指明类型:**
* 1、显示声明 let a:string;
* 2、隐式声明 let a=12;
* 如何让变量什么都能装?  1、不给初始值 2、显示声明

>number / string / boolean / any
void用于标识方法返回值的类型，表示该方法没有返回值
null表示对象值缺失/undefined用于初始化变量为一个未定义的值
never是其它类型（包括 null 和 undefined）的子类型，代表从不会出现的值
数组: (类型1|类型2|类型3)[]
元组: [类型1,类型2]
枚举: enum(枚举类型用于定义数值集合)
```ts
enum Color {Red, Green, Blue};
let c: Color = Color.Blue;
console.log(c);//2

let a:number[] = [1, 3, 5];
let a:Array<number> = [1, 2, 5];
```

## 2、类
**访问修饰符`public/private/protected`**
* public: 任何人都能修改
* private: 只有类内部能使用
* protectd: 只有类和子类能使用

**访问修饰符怎么用?**
* "最小访问原则"--尽可能使用private
* 属性都是private或protected
* 方法一般都用public

**修饰符**
* static 静态成员——无需实例化即可调用
* const  只读成员——只读，不能修改

**存取器**
* set/get 遍历不了ES3、4 需要tsc -t es5 12存取器.ts

## 3、高级特性-抽象、接口
**一些对象具有共同的特性:**
* 继承
* 实现接口

>抽象类——只提供模板，而不自己实现--规范所有子类. 接口、抽象为了规范所有子类


