---
title: 水滴图
date: 2020-12-24 09:29:36
tag: sass
---

**先画出外形**

![1](/assets/sassImg/2-1.png "球状")

```html
<div class="state-ball">
    <div class="wave"></div>
</div>
```

```scss
.state-ball {
    overflow: hidden;
    position: relative;
    padding: 5px;
    border: 3px solid #3c9;
    border-radius: 100%;
    width: 150px;
    height: 150px;
    background-color: #fff;
    .wave {
        position: relative;
        border-radius: 100%;
        width: 100%;
        height: 100%;
        background-image: linear-gradient(to bottom, #af8 13%, #3c9 91%);
    }
}
```

**进度通常都是从底部往顶部逐渐提升，可用`::before`绘制一个圆形遮罩层，进度变化时将遮罩层一直往上提升产生障眼效果。提升过程可用绝对定位将遮罩层固定在底部，通过调整`margin-bottom`平移遮罩层**

**为了方便演示，注释父容器的`overflow:hidden`，通过`Chrome Devtools`微调`margin-bottom`看看整体效果。后续记得将`overflow:hidden`声明回来。**

![2](/assets/sassImg/2-2.gif "::before")
```scss
.state-ball {
    // overflow: hidden;
    // ...
    &::before {
        position: absolute;
        left: 50%;
        bottom: 5px;
        z-index: 9;
        margin-left: -100px;
        margin-bottom: 0;
        border-radius: 100%;
        width: 200px;
        height: 200px;
        background-color: #09f;
        content: "";
    }
    // ...
}
```
