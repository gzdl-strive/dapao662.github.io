---
title: CSS3圆角
date: 2020-12-31 10:27:19
tag: CSS
---

```html
<style>
    div {
        width: 100px;
        height: 100px;
        background-color: darkgray;
        margin: 10px auto 0;
    }
    .d1 {
        border-radius: 10px/50px;
    }
    .d2 {
        border-radius: 10px 50px;
    }
    .d3 {
        border-radius: 10px 50px 40px;
    }
</style>
<body>
    <div class="d1"></div>
    <div class="d2"></div>
    <div class="d3"></div>
</body>
```

![1](/assets/css3Img/1.png "CSS3圆角")