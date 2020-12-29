---
title: 矩形
date: 2020-12-29 14:09:19
tag: HTML
---

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
</body>
<script>
window.onload = function () {
    let oC = document.getElementById('c1');
    let gd = oC.getContext('2d');
    gd.rect(100, 100, 100, 50);//在坐标100,100位置画出一个长100，宽50的矩形
    gd.stroke();

    gd.strokeStyle = 'yellow';
    gd.strokeRect(300, 300, 80, 50);

    gd.fillStyle = 'green';
    gd.fillRect(100, 300, 80, 50);
}
</script>
```

![3](/assets/html5Img/canvasImg/3.png "3")

