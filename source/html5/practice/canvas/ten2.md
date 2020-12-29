---
title: 图片2
date: 2020-12-29 16:13:19
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
            gd.drawImage(oImg,
                // sx,sy,sw,sh  source 
                100, 50, 70, 100,
                //  dx,dy,dw,dh  dest
                300, 200, 140, 200
            );
        }
    }
</script>
```

![10-2](/assets/html5Img/canvasImg/10-2.png "10-2")