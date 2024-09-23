---
title: 图片
date: 2020-12-29 16:10:19
tag: HTML
---

>语法一: context.drawImage(img,x,y);
语法二: context.drawImage(img, x, y, width, height);
语法三: context.drawImage(img, sx, sy, swidth, sheight, x, y, widht, height);
* img       规定要使用的图像、画布或视频
* sx        可选。开始剪切的x坐标位置。
* sy        可选。开始剪切的 y 坐标位置。
* swidth    可选。被剪切图像的宽度。
* sheight   可选。被剪切图像的高度。
* x         在画布上放置图像的 x 坐标位置。
* y         在画布上放置图像的 y 坐标位置。
* width     可选。要使用的图像的宽度。（伸展或缩小图像）
* height    可选。要使用的图像的高度。（伸展或缩小图像）


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