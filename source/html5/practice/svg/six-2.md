---
title: js创建svg-pie2
date: 2020-12-31 9:18:19
tag: SVG
---

>JS生成元素
document.createElement  创建HTML元素
NS-->Name Space(document.createElementNS('http://www.w3.org/2000/svg','line'))
document.createElement=>document.createElementNS("http://www.w3.org/1999/xhtml")
如果要创建svg标签也要用NS

```html
<body>
    <svg width="800" height="600" id="svg1"></svg>
</body>
<script>
    function d2a(n) {
        return n * Math.PI / 180;
    }
    window.onload = function() {
        let oSvg = document.getELementById('svg1');
        let cx = 400, cy = 300, r = 200;

        let data = [2000, 500, 360, 1500, 600, 1000];
        let sum = 0;
        data.forEach(item => sum += item);
        let now = 0;
        data.forEach(item => {
            let ang = 360 * item / sum;
            let color = Math.floor(Math.random() * 16777216).toString(16);//FFFFFF十进制16777216，转成16进制
            while(color < 6) {
                color = '0' + color;
            }
            pie(now, now + ang, '#' + color);
            now += ang;
        });
        function pie(ang1, ang2, color) {
            let oP = document.createElementNS('http://www.w3.org/2000/svg', 'path');
            oP.style.fill = color;

            oSvg.appendChild(oP);
            let arr = [];
            function getPoint(ang) {
                return {
                    x: cx + Math.sin(d2a(ang)) * r,
                    y: cy + Math.cos(d2a(ang)) * r,
                }
            }
            // #1
            let {x: x1, y: y1} = getPoint(ang1);
            arr.push(`M ${cx, cy} L ${x1} ${y1}`);
            // #2
            let {x: x2, y: y2} = getPoint(ang2);
            // path A rx ry x旋转 大弧标志 镜像标志 终点x y
            arr.push(`A ${r} ${r} 0 ${ang2 - ang1 >= 180 ? 1 : 0} 1 ${x2} ${y2}`);
            // #3
            arr.push(`Z`);

            oP.setAttribute('d', arr.join(' '));//以" "拼接数组中各元素
        }
    }
</script>
```

![6-2](/assets/html5Img/svgImg/6-2.png "6-2")