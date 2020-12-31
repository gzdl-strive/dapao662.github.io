---
title: CSS3transform
date: 2020-12-31 10:51:19
tag: CSS
---

>perspective透视(越小3d感觉越明显)，默认不给是无限大

```html
<style>
    .d1 {
        width: 100px;
        height: 100px;
        background: darkgray;
        margin: 10px auto 0;
        transition: 1s all ease;
        transform: perspective(200px) rotateX(0deg);
    }
    .d1:hover {
        transform: perspective(200px) rotateX(60deg);
    }
</style>
<body>
    <div class="d1"></div>
</body>
```

![4](/assets/css3Img/4.gif "CSS3转换")