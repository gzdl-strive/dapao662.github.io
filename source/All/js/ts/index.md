---
title: TS及其练习
toc: true
date: 2021-11-03 15:03:02
---

## TypeScript 简介

1. TypeScript 是 JavaScript 的超集。
2. 它对 JS 进行了扩展，向 JS 中引入了类型的概念，并添加了许多新的特性。
3. TS 代码需要通过编译器编译为 JS，然后再交由 JS 解析器执行。
4. TS 完全兼容 JS，换言之，任何的 JS 代码都可以直接当成 JS 使用。
5. 相较于 JS 而言，TS 拥有了静态类型，更加严格的语法，更强大的功能；TS 可以在代码执行前就完成代码的检查，减小了运行时异常的出现的几率；TS 代码可以编译为任意版本的 JS 代码，可有效解决不同 JS 运行环境的兼容问题；同样的功能，TS 的代码量要大于 JS，但由于 TS 的代码结构更加清晰，变量类型更加明确，在后期代码的维护中 TS 却远远胜于 JS。

# TS基本认识和配置

## 1、TypeScript基本类型
>[基本类型](/All/js/ts/basicType "基本类型")

## 2、编译选项
>[tsconfig.json编译选项](/All/js/ts/tsconfig "编译选项")

## 3、webpack配置
>[webpack配置](/All/js/ts/webpack "webpack配置")

## 4、Babel
>[Babel](/All/js/ts/babel "babel")

# TS面向对象
面向对象是程序中一个非常重要的思想，它被很多同学理解成了一个比较难，比较深奥的问题，其实不然。面向对象很简单，简而言之就是程序之中所有的操作都需要通过对象来完成。

- 举例来说：
  - 操作浏览器要使用window对象
  - 操作网页要使用document对象
  - 操作控制台要使用console对象

一切操作都要通过对象，也就是所谓的面向对象，那么对象到底是什么呢？这就要先说到程序是什么，计算机程序的本质就是对现实事物的抽象，抽象的反义词是具体，比如：照片是对一个具体的人的抽象，汽车模型是对具体汽车的抽象等等。程序也是对事物的抽象，在程序中我们可以表示一个人、一条狗、一把枪、一颗子弹等等所有的事物。一个事物到了程序中就变成了一个对象。

在程序中所有的对象都被分成了两个部分数据和功能，以人为例，人的姓名、性别、年龄、身高、体重等属于数据，人可以说话、走路、吃饭、睡觉这些属于人的功能。数据在对象中被成为属性，而功能就被称为方法。所以简而言之，在程序中一切皆是对象。

## 1、类(class)
>[类class](/All/js/ts/class "类")

## 2、面向对象的特点
>[面向对象的特点](/All/js/ts/special "面向对象的特点")

## 3、接口(Interface)
>[接口](/All/js/ts/interface "接口")

## 4、泛型(Generic)
>[泛型](/All/js/ts/generic "泛型")

# TS练习-贪吃蛇
## 1、环境搭建
>[环境搭建,配置](/All/js/ts/environment "环境搭建")

**根目录下新建src文件夹,里面添加index.html和index.ts文件**
**src中新建style目录，style中新建index.less样式文件**

## 2、静态页面
>[静态页面](/All/js/ts/page "静态页面")

**src下新建modules目录，用于存放我们的各个模块代码**

## 3、模块功能实现
>[1、食物模块](/All/js/ts/food "食物模块")
>[2、记分器模块](/All/js/ts/scorePanel "记分器模块")
>[3、蛇模块](/All/js/ts/snake "蛇模块")
>[4、控制器模块](/All/js/ts/gameControl "控制器模块")

**修改入口文件index.ts,启动**
```ts
//引入样式
import './style/index.less';

import GameControl from './modules/GameControl';
//调用控制器模块，控制器模块中进行初始化等操作
new GameControl();
```