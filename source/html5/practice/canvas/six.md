---
title: 圆
date: 2020-12-29 14:39:19
tag: HTML
---

>beginPath()  清楚之前路径
gd.arc(圆心x,y,半径r,起始弧度，终止弧度，顺时针false还是逆时针true)

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
    //角度转弧度--360度 = 2 * Math.PI -> 1度 = 2 * Math.PI / 360 = Math.PI / 180
    function d2a(n) {
        return n * Math.PI / 180;
    }
    //弧度转角度--360度 = 2 * Math.PI -> 1弧度 = 180 / Math.PI
    function a2d(n) {
        return n * 180 / Math.PI;
    }
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');
        let cx = 280, cy = 280, r = 100;

        gd.beginPath();
        //arc(圆心x,y,半径r,起始弧度，终止弧度，顺时针false还是逆时针true)
        // 因为要用到弧度需要使用角度转化为弧度
        gd.arc(cx, cy, r, d2a(0), d2a(360), true);
        gd.stroke();

        oC.onmousemove = function(ev) {
            let x = ev.offsetX, y = ev.offsetY;
            //点到圆心的距离如果小于半径就在圆内
            //点到圆心的距离 = 三角形斜边计算公式
            let dis = Math.sqrt(Math.pow(x - cx, 2), Math.pow(y - cy, 2));
            gd.clearRect(0, 0, oC.width, oC.height);
            if (dis <= r) {
                gd.strokeStyle = 'red';
            } else {
                gd.strokeStyle = 'black';
            }
            gd.beginPath();
            gd.arc(cx, cy, r, d2a(0), d2a(360), true);
            gd.stroke();
        }
    }
</script>
```

![6](/assets/html5Img/canvasImg/6.gif "6")
