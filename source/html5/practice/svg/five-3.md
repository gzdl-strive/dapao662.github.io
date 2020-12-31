---
title: pie
date: 2020-12-29 14:31:19
tag: HTML
---

```html
<body>
    <svg><path id="p1" d=" " style="stroke: red;fill: none"></path></svg>
</body>
<script>
function d2a(n) {
    return n * Math.PI / 180;
}
window.onload = function() {
    let oP = document.getElementById('p1');
    let cx = 400, cy = 300, r = 200;
    let ang1 = 0, ang2 = 160;
    let arr = [];

    function getPoint(ang) {
        return {
            x: cx + Math.sin(d2a(ang)) * r,
            y: cy - Math.cos(d2a(ang)) * r,
        }
    }

    // #1
    let {x: x1, y: y1} = getPoint(ang1);
    arr.push(`M ${cx} ${cy} L ${x1} ${y1}`);
    // #2
    let {x: x2, y: y2} = getPoint(ang2);
    // path A rx ry x旋转 大弧标志 镜像标志  终点x y
    arr.push(`A ${r} ${r} 0 ${ang2 - ang1 >= 180 ? 1 : 0} 1 ${x2} ${y2}`);
    // #3
    arr.push('Z');
    oP.setAttribute('d', arr.join(' '));
}
</script>
```

![5-3](/assets/html5Img/svgImg/5-3.png "5-3")