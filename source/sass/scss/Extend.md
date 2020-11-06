---
title: Extend(继承)
date: 2020-11-06 20:44:24
tag: scss
---

>Extend is a way to share the properties of one selector with another

```scss
@mixin size($width, $height) {
    width: $width;
    height: $height;
}
.display {
    @include size(5em, 5em);
    border: 5px solid $secondary-color;
}

.display-success {
    @extend .display;
    border-color: #22df56;
}

/* Compiles to: */
.display, .display-success {
  width: 5em;
  height: 5em;
  border: 5px solid #51527F;
}

.display-success {
  border-color: #22df56;
}
```
>Extending CSS语句比创建mixin更可取，因为Sass将所有共享相同基本样式的类组合在一起的方式。如果是用mixin完成的，则宽度，高度和边框将为每个调用mixin的语句复制。尽管它不会影响您的工作流程，但会为Sass编译器创建的文件增加不必要的膨胀。

