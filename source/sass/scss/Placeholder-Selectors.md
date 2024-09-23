---
title: Placeholder Selectors(占位符选择器)
date: 2020-11-06 21:19:24
tag: scss
---

>创建CSS语句进行扩展时，占位符非常有用。如果你想要创建专用于@extend的CSS语句，可以使用占位符。占位符以`％`而不是`.`开头或者`＃`。占位符将不会出现在已编译的CSS中。

```scss
%content-window {
  font-size: 14px;
  padding: 10px;
  color: #000;
  border-radius: 4px;
}

.message-window {
  @extend %content-window;
  background-color: #0000ff;
}

/*Compiles to:*/
.message-window {
  font-size: 14px;
  padding: 10px;
  color: #000;
  border-radius: 4px;
}

.message-window {
  background-color: #0000ff;
}

```
