---
title: 饼图
date: 2020-12-29 15:04:19
tag: HTML
---

```html
<style>
    body{
        background-color: black;
        text-align: center;
    }
    #c1{
        background-color: #ccc;
    }
</style>
<body>
    <canvas id="c1" width="800" height="600"></canvas>
</body>
<script>
    function d2a(n) {
        return n * Math.PI / 180;
    }
    function a2d(n) {
        return n * 180 / Math.PI;
    }
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');
        let cx = 400, cy = 300, r = 200;
        let datas = [
            { data: 300, color: '#fc0'},
            { data: 200, color: '#cf0'},
            { data: 40, color: '#cc0'},
        ];
        function pie(startAng, endAng, color) {
            let x = cx + Math.cos(d2a(startAng)) * r;
            let y = cy + Math.sin(d2a(startAng)) * r;
            gd.beginPath();
            //#1 圆心到对应角度的线
            gd.moveTo(cx, cy);
            gd.lineTo(x, y);
            //#2 画出对应区域
            gd.arc(cx, cy, r, d2a(startAng), d2a(endAng), false);
            //#3
            gd.closePath();
            gd.fillStyle = color;
            gd.fill();
        }
        //总和
        let sum = 0;
        datas.forEach(data => {
            sum += data.data;
        });
        let now = 0;
        datas.forEach(data => {
            let ang = data.data / sum * 360;
            pie(now, now + ang, data.color);
            now += ang;
        });
    }
</script>
```

![7](/assets/html5Img/canvasImg/7.png "7")

