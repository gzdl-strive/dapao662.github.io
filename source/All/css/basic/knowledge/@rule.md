---
title: 规则(@)
date: 2021-03-29 16:39:20
tog: css
toc: true
---

## @规则
>CSS 规则是样式表的主体，通常样式表会包括大量的规则列表。但有时候也需要在样式表中包括其他的一些信息，比如字符集，导入其它的外部样式表，字体等，这些需要专门的语句表示。

而 @规则 就是这样的语句。CSS 里包含了以下 @规则：
* @namespace 告诉 CSS 引擎必须考虑XML命名空间。
* @media, 如果满足媒体查询的条件则条件规则组里的规则生效。
* @page, 描述打印文档时布局的变化.
* @font-face, 描述将下载的外部的字体。
* @keyframes, 描述 CSS 动画的关键帧。
* @document, 如果文档样式表满足给定条件则条件规则组里的规则生效。 (推延至 CSS Level 4 规范)
除了以上这几个之外，下面还将对几个比较生涩的 @规则 进行介绍。

### @charset
@charset 用于定义样式表使用的字符集。它必须是样式表中的第一个元素。如果有多个 `@charset` 被声明，只有第一个会被使用，而且不能在HTML元素或HTML页面的 `<style>` 元素内使用。
注意：值必须是双引号包裹，且和
```css
@charset "UTF-8";
```
平时写样式文件都没写 @charset 规则，那这个 CSS 文件到底是用的什么字符编码的呢？
某个样式表文件到底用的是什么字符编码，浏览器有一套识别顺序（优先级由高到低）：
* 文件开头的 `Byte order mark` 字符值，不过一般编辑器并不能看到文件头里的 BOM 值；
* HTTP 响应头里的 `content-type` 字段包含的 `charset` 所指定的值，比如：
```html
Content-Type: text/css; charset=utf-8
```
* CSS 文件头里定义的 @charset 规则里指定的字符编码；
* `<link>` 标签里的 charset 属性，该条已在 HTML5 中废除；
* 默认是 UTF-8。

### @import
`@import` 用于告诉 CSS 引擎引入一个外部样式表。

**link 和 @import 都能导入一个样式文件，它们有什么区别嘛？**

* link 是 HTML 标签，除了能导入 CSS 外，还能导入别的资源，比如图片、脚本和字体等；而 @import 是 CSS 的语法，只能用来导入 CSS；
* link 导入的样式会在页面加载时同时加载，@import 导入的样式需等页面加载完成后再加载；
* link 没有兼容性问题，@import 不兼容 ie5 以下；
* link 可以通过 JS 操作 DOM 动态引入样式表改变样式，而@import不可以。

### @supports
`@supports `用于查询特定的 CSS 是否生效，可以结合 not、and 和 or 操作符进行后续的操作。
```css
/* 如果支持自定义属性，则把 body 颜色设置为变量 varName 指定的颜色 */
@supports (--foo: green) {
    body {
        color: var(--varName);
    }
}
```