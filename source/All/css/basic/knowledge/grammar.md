---
title: 语法
date: 2021-03-29 16:23:20
tog: css
toc: true
---

## 语法
CSS的核心功能是将CSS属性设定为特定的值。一个属性与值的键值对被称为**声明**(declaration).
```css
color: red;
```
而如果将一个或者多个声明用 {} 包裹起来后，那就组成了一个**声明块**(declaration block).
```css
{
    color: red;
}
```
声明块如果需要作用到对应的 HTML 元素，那还需要加上**选择器**。选择器和声明块组成了**CSS 规则集**（CSS ruleset），常简称为 CSS 规则。
![2](/assets/cssImg/allBasic/2.png "CSS规则集-ruleset")
```css
span {
    color: red;
    text-align: center;
}
```
>规则集中最后一条声明可以省略分号，但是并不建议这么做，因为容易出错。

### 注释
CSS中的**注释**:
```css
/*单行注释*/
/*
    多行
    注释
*/
```
在 CSS 文件中，除了注释、CSS 规则集以及 @规则 外，定义的一些别的东西都将被浏览器忽略。

