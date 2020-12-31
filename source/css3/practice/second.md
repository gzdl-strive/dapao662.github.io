---
title: CSS3阴影
date: 2020-12-31 10:35:19
tag: CSS
---

>text-shadow: 横向偏移 纵向偏移 阴影像素 颜色

>box shadow: 横向偏移 纵向偏移 阴影像素 颜色

```html
<style>
    div {
        width: 300px;
        height: 300px;
        background-color: darkgray;
        margin: 10px auto 0;
    }
    .d1 {
        /* text-shadow: 横向偏移 纵向偏移 阴影像素 颜色 */
        text-shadow: 5px 10px 2px red;
    }
    .d2 {
        /* box shadow: 横向偏移 纵向偏移 阴影像素 颜色 */
        box-shadow: 2px 2px 2px black;
    }
    .d2:active {
        box-shadow: inset 2px 2px 2px black;
    }
    .d3 {
        /*box-shadow:横向偏移 纵向偏移 阴影像素 扩展阴影 颜色*/
        box-shadow: 2px 2px 2px 20px black;
    }
</style>
<body>
    <div class="d1">
        这是一些字
    </div>
    <div class="d2">
        这是一些字
    </div>
    <br />
    <div class="d3">
        这是一些字
    </div>
</body>
```

![2](/assets/css3Img/2.png "CSS3阴影")