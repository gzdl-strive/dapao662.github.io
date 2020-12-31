---
title: CSS3渐变
date: 2020-12-31 10:39:19
tag: CSS
---

>渐变只能用background/background-image（是一个图片）

```html
<style>
    div {
        width: 100px;
        height: 100px;
        background: darkgray;
        margin: 10px auto 0;
    }
    .d1 {
        background: -webkit-linear-gradient(left top, red, greenyellow);
    }
    .d2 {
        /* background: -webkit-linear-gradient(red 50%,greenyellow 50%); */
        background: -webkit-linear-gradient(left, greenyellow 40%, blue);
    }
    .d3 {
        background: -webkit-radial-gradient(red, greenyellow);
    }
</style>
<body>
    <div class="d1"></div>
    <div class="d2"></div>
    <hr />
    <div class="d3"></div>
</body>
```
![3](/assets/css3Img/3.png "CSS3渐变")