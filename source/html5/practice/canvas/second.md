---
title: 清除路径
date: 2020-12-29 11:28:19
tag: HTML
---

>beginPath() 清除之前的路径，重新开始

```html
<style>
    body{
        background-color: black;
        text-align: center;
    }
    #c1{
        background-color: wheat;
    }
</style>
<body>
    <!--属性里面带宽高  img/canvas/iframe-->
    <!-- 与img一样是个行内块受text-align支配   -->
    <canvas id="c1" width="800" height="600"></canvas>
    <script>
    window.onload=function () {
        let oC=document.getElementById('c1');
        let gd=oC.getContext('2d');

        gd.moveTo(100,100);
        gd.lineTo(200,200);
        gd.strokeStyle='red';
        gd.stroke();

        gd.beginPath();     //清楚之前的路径，重新开始
        gd.moveTo(200,100);
        gd.lineTo(300,200);
        gd.strokeStyle='green';
        gd.stroke();
    }
    </script>
</body>
```

![2](/assets/html5Img/canvasImg/2.png "2")