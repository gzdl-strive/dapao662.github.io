---
title: 像素灰
date: 2020-12-29 16:38:19
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
            let data = imageData.data;
            //黄色=>扔掉蓝色(红+绿)
            //灰色=>三色平均
            for (let r = 0; r < h; r++) {
                for (let c = 0; c < w; c++) {
                    //(r*w+c)*4+0       =>  r
                    //(r*w+c)*4+1       =>  g
                    //(r*w+c)*4+2       =>  b
                    //(r*w+c)*4+3       =>  a
                    data[(r*w+c)*4+0]=data[(r*w+c)*4+1]=data[(r*w+c)*4+2]=
                            /*0.5可以调亮度**/(data[(r*w+c)*4+0]+data[(r*w+c)*4+1]+data[(r*w+c)*4+2])/3;
                }
            }
            gd.putImageData(imageData, 0, 0);
        }
    }
</script>
```

![11-2](/assets/html5Img/canvasImg/11-2.png "11-2")