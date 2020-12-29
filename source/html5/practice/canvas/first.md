---
title: 初步使用
date: 2020-12-29 11:13:19
tag: HTML
---

>context--上下文
图形上下文-接口 所有图形方法、属性
`getContext('2d')`对象是内建的HTML5对象，拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法
fillStyle 方法将其染成某种颜色，fillRect 方法规定了形状、位置和尺寸
fillRect 方法拥有参数 (0,0,150,75)-->在画布上绘制 150x75 的矩形，从左上角开始 (0,0)

```html
<style>
    body {
        background-color: #000;
        text-align: center;
    }
    #c1 {
        background-color: wheat;
    }
</style>
<!-- 属性里面带宽高 img、canvas、iframe -->
<!-- 与img一样是个行内块受text-align支配 -->
<canvas id="c1" width="800" height="600"></canvas>
<script>
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');

        // gd.fillStyle = '#f00';
        gd.strokeStyle = '0f0';

        gd.moveTo(50, 50);
        gd.lineTo(150, 150);
        gd.lineTo(100, 200);
        gd.lineTo(10, 150);
        //闭合有两种方法
        //第一种: gd.lineTo(50, 50);
        //第二种
        gd.closePath();

        gd.lineWidth = 10;
        gd.stroke();
        //gd.fill();
    }
</script>
```

![1](/assets/html5Img/canvasImg/1.png "1")