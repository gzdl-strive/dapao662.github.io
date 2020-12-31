---
title: svg样式
date: 2020-12-30 10:11:19
tag: SVG
---

>SVG标签：
    只能做属性--决定图形形状
    可以放样式（style）--视觉上效果-颜色...
    *放样式里
    **属性的样式优先级太低了
    属性<*<标签<class<ID<行间


```html
<body>
    <svg widht="800" height="500">
        <line x1="100" y1="100" x2="300" y2="300" stroke="black" stroke-width="20"></line>
        <line x1="200" y1="100" x2="400" y2="300" style="stroke: greenyellow; stroke-width: 10"></line>
    </svg>
</body>
```

![2](/assets/html5Img/svgImg/2.png "2")