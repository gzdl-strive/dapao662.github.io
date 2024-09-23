---
title: path2
date: 2020-12-30 17:14:19
tag: HTML
---

>M = moveto
L = lineto
H = horizontal lineto
V = vertical lineto
C = curveto
S = smooth curveto
Q = quadratic Belzier curve
T = smooth quadratic Belzier curveto
A = elliptical Arc
Z = closepath
`<path>`
M   moveTo  x y
L   lineTo  (x y)+
A   arc     rx ry x旋转 大弧标志(大弧：1,小弧0) 镜像标志（起点终点连线，左：0，右：1）  终点x y



```html
<body>
    <svg width="800" height="600">
        <path d="M 200 300 L 300 300 A 100 100 0 0 1 500 300 L 600 300" style="stroke: red;fill:none"></path>
    </svg>
</body>
```

![5-2](/assets/html5Img/svgImg/5-2.png "5-2")