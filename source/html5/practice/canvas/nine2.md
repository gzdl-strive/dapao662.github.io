---
title: 转换2
date: 2020-12-29 15:47:19
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
    <canvas id="c1" width="800" height="600"></canvas>
</body>
<script>
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');

        //跟css3一样，从后往前
        gd.translate(200, 175);
        gd.rotate(30 * Math.PI / 180);//先转再移动
        gd.strokeRect(-100, -75, 200, 150);//相当于以自己为中心转
    }
</script>
```
![9-2](/assets/html5Img/canvasImg/9-2.png "9-2")