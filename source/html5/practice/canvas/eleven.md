---
title: 像素
date: 2020-12-29 16:29:19
tag: HTML
---

```html
<style>
    body{
        background-color: white;
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
        let w = oC.width;
        let h = oC.height;

        let oImg = new Image(); //相当于let oImg = document.createElement('img');
        oImg.src = "1.jpeg";
        //drawImage(图片, x, y)
        oImg.onload = function() {
            gd.drawImage(oImg, 0, 0);
            let imageData = gd.getImageData(0, 0, w, h);
            //页面有w*h个像素===800*600,但实际上imageData却有4*w*h个，因为一个像素占4位(r,g,b,a);
            //第0行第0列 --》0
            //第0行第79列 --> 79
            //第1行第2列 --》 1*w+2=w+2
            //第r行第c列 --> w*r+c
            //黄色=>扔掉蓝色(红+绿)
            for (let r = 0; r < h; r++) {
                for (let c = 0; c < w; c++) {
                    //r*w+c的第r行第c列像素,(r*w+c)*4是因为imageData里每4个表示一个像素，所以要乘4表示下一个像素，(r*w+c)+0表示当前像素的第一位-也就是r,依次类推
                    //(r*w+c)*4+0       =>  r
                    //(r*w+c)*4+1       =>  g
                    //(r*w+c)*4+2       =>  b
                    //(r*w+c)*4+3       =>  a
                    imageData.data[(r * w + c) * 4 + 2] = 0;
                }
            }
            gd.putImageData(imageData, 0, 0);
        }
    }
</script>
```

![11-1](/assets/html5Img/canvasImg/11-1.png "11-1")