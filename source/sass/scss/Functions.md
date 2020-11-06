---
title: Functions(函数)
date: 2020-11-06 17:53:24
tag: scss
---

>`Sass `定义了各种类型的函数，这些函数我们可以通过` CSS `语句直接调用。函数可以通过使用它们的名称并传递所需的参数来调用.

```scss
body {
  width: round(10.25px);
}
.footer {
    background-color: fade_out(#000000, 0.25);
}
/*Compiles to : */
body {
    width: 10px;
}
.footer {
    background-color: rgba(0, 0, 0, 0.75);
}
```
>您也可以定义自己的函数。功能与mixin非常相似。当尝试在函数或混合输入之间进行选择时，请记住混合输入最适合生成CSS，而函数则更适合可能在整个Sass代码中使用的逻辑。“算术运算符”部分中的示例是成为可复用  功能的理想候选者

**此函数将采用目标大小和父大小，并计算并返回百分比**
```scss
@function calculate-percentage($target-size, $parent-size) {
    @return $target-size / $parent-size * 100%;
}
$main-content: calculate-percentage(600px, 960px);
.main-content {
    width: $main-content;
}
.sidebar {
    width: calculate-percentage(300px, 960px);
}
/*Compiles to :*/
.main-content {
  width: 62.5%;
}
.sidebar {
  width: 31.25%;
}
```