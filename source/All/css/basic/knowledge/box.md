---
title: 盒模型
date: 2021-03-29 17:40:20
tog: css
toc: true
---

在 CSS 中任何元素都可以看成是一个盒子，而一个盒子是由 4 部分组成的：内容（content）、内边距（padding）、边框（border）和外边距（margin）。
盒模型有 2 种：标准盒模型和 IE 盒模型，本别是由 W3C 和 IExplore 制定的标准。
如果给某个元素设置如下样式：
```css
.box {
    width: 200px;
    height: 200px;
    padding: 10px;
    border: 1px solid #eee;
    margin: 10px;
}
```
**标准盒模型认为：盒子的实际尺寸 = 内容（设置的宽/高） + 内边距 + 边框**
![5](/assets/cssImg/allBasic/5.png "标准盒模型(box-sizing: content-box)")
所以 `.box` 元素内容的宽度就为 `200px`，而实际的宽度则是 `width` + `padding-left` + `padding-right` + `border-left-width` + `border-right-width` = 200 + 10 + 10 + 1 + 1 = 222。

**IE 盒模型认为：盒子的实际尺寸 = 设置的宽/高 = 内容 + 内边距 + 边框**
![6](/assets/cssImg/allBasic/6.png "IE盒模型(box-sizing: border-box)")
`.box` 元素所占用的实际宽度为 `200px`，而内容的真实宽度则是` width` - `padding-left` -` padding-right` - `border-left-width` - `border-right-width` = 200 - 10 - 10 - 1 - 1 = 178。
现在高版本的浏览器基本上默认都是使用标准盒模型，而像 IE6 这种老古董才是默认使用 IE 盒模型的。

在  CSS3 中新增了一个属性 `box-sizing`，允许开发者来指定盒子使用什么标准，它有 2 个值：
* `content-box`：标准盒模型；
* `border-box`：IE 盒模型；
