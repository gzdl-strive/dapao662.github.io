---
title: 图片
date: 2020-12-29 16:10:19
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

        let oImg = new Image(); //相当于let oImg = document.createElement('img');
        oImg.src = "1.jpg";
        //drawImage(图片, x, y)
        oImg.onload = function() {
            //当图片加载完才能显示
            gd.drawImage(oImg, 0, 0);
        }
    }
</script>
```

![10-1](/assets/html5Img/canvasImg/10-1.png "10-1")