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
            //黄色=>扔掉蓝色(红+绿)
            for (let r = 0; r < h; r++) {
                for (let c = 0; c < w; c++) {
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