---
title: 图片3-行走图
date: 2020-12-29 16:16:19
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

        let oImg = new Image();//相当于document.createElement('img');
        oImg.src = '2.png';
        let i = 0;      //图片切换
        let x = 100;    //位置改变
        let frameCount = 0;//几帧走一次
        let pause = false; //点击切换

        oImg.onload = function() {
            requestAnimationFrame(next);
            fucntion next() {
                if (!pause) {
                    gd.clearRect(0, 0 oC.width, oC.height);
                    if (frameCount % 6 === 0) {
                        i++;
                        if (i === 9) {
                            i = 0;
                        }
                    }
                    gd.drawImage(oImg,
                        // sx,sy,sw,sh  source
                        i * 35, 0, 35, 38,
                        //  dx,dy,dw,dh  dest
                        x, 200, 35, 38
                    );
                    x++;
                    frameCount ++;
                }
            }
        }
        document.onclick = function() {
            pause = !pause;
        }
    }
</script>
```
![10](/assets/html5Img/canvasImg/10-3.gif "10-3")