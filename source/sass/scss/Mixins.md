---
title: Mixins(混合)
date: 2020-11-06 17:30:24
tag: scss
---

>如果发现要为多个元素编写相同的代码，则可能需要将该代码存储在`mixin`中。使用`@mixin指令`，以及您的`mixin名称`。

```scss
$primary-color: #A4A3FF;
@mixin center {
    display: block;
    margin-left: auto;
    margin-right: auto;
    left: 0;
    right: 0;
}
/*您可以将mixin与'@include'和mixin名称一起使用*/
div {
    @include center;
    background-color: $primary-color;
}
/*which would compile to: */
div {
    display: block;
    margin-left: auto;
    margin-right: auto;
    left: 0;
    right: 0;
    background-color: #A4A3FF;
}
/*------------------------------------*/

/*您可以使用mixins创建速记属性*/
@mixin size($width, $height) {
    width: $width;
    height: $height;
}
/*您可以通过传递width和height参数来调用它*/
.rectangle {
    @include size(100px, 60px);
}
.square {
    @include size(40px, 40px);
}
/*Compiles to :*/
.rectangle {
  width: 100px;
  height: 60px;
}
.square {
  width: 40px;
  height: 40px;
}
```

