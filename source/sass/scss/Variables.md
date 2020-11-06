---
title: 变量(Variables)
date: 2020-11-06 16:05:02
tag: scss
---

>你可以存储`CSS`值(例如颜色)作为一个变量。使用`$`符号创建变量

```scss
$primary-color: #A3A4FF;
$secondary-color: #51527F;
$body-font: "Roboto", sans-serif;
```

>你可以在整个样式表中使用变量。现在，如果要更改颜色，只需更改一次即可.

```scss
body {
    background-color: $primary-color;
    color: $secondary-color;
    font-family: $body-font;
}
```
**这将编译为：(This Would compile to:)**
```scss
body {
    background-color: #A3A4FF;
    color: #51527F;
    font-family: 'Roboto', sans-serif; 
}
```