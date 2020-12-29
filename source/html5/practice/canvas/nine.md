---
title: 转换
date: 2020-12-29 15:41:19
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

        //gd.rotate要写在strokeRect之前-就像strokeStyle一样--在填充之前的操作
        gd.rotate(30 * Math.PI / 180);
        //canvas里面不会单独处理某一个图形，所以转换了的话会是整个画布一起转换
        gd.strokeRect(100, 100, 200, 150);

        gd.fillStyle = 'rgba(255, 0, 0, 0.5)';
        gd.fillRect(0, 0, oC.width, oC.height);
    }
</script>
```

![9-1](/assets/html5Img/canvasImg/9-1.png "9-1")