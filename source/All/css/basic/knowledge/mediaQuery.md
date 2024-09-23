---
title: 媒体查询
date: 2021-03-30 09:28:20
tog: css
toc: true
---

媒体查询是指针对不同的设备、特定的设备特征或者参数进行定制化的修改网站的样式。
你可以通过给 `<link>` 加上 media 属性来指定该样式文件只能对什么设备生效，不指定的话默认是 all，即对所有设备都生效：
```html
<link rel="stylesheet" src="styles.css" media="screen" />
<link rel="stylesheet" src="styles.css" media="print" />
```
都支持哪些设备类型？
* all：适用于所有设备；
* print：适用于在打印预览模式下在屏幕上查看的分页材料和文档；
* screen：主要用于屏幕；
* speech：主要用于语音合成器。
>需要注意的是：通过 media 指定的 资源尽管不匹配它的设备类型，但是浏览器依然会加载它。

![23](/assets/cssImg/allBasic/23.png)
除了通过 `<link>` 让指定设备生效外，还可以通过 `@media `让 CSS 规则在特定的条件下才能生效。响应式页面就是使用了 @media 才让一个页面能够同时适配 PC、Pad 和手机端。
```css
@media (min-width: 1000px) {}
```
媒体查询支持逻辑操作符：
* and：查询条件都满足的时候才生效；
* not：查询条件取反；
* only：整个查询匹配的时候才生效，常用语兼容旧浏览器，使用时候必须指定媒体类型；
* 逗号或者 or：查询条件满足一项即可匹配；
媒体查询还支持众多的媒体特性，使得它可以写出很复杂的查询条件：
```css
/* 用户设备的最小高度为680px或为纵向模式的屏幕设备 */
@media (min-height: 680px), screen and (orientation: portrait) {}
```




