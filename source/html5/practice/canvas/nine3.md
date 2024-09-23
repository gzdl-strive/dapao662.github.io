---
title: 转换3
date: 2020-12-29 15:55:19
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
        let r = 0;

        requestAnimationFrame(next);
        function next() {
            gd.clearRect(0, 0, oC.width, oC.height);
            r++;

            gd.save();//保存：canvas的状态(图形不报存)-保存颜色、tanslate\rotate
            gd.translate(200, 175);
            gd.rotate(r * Math.PI / 180);
            gd.strokeStyle = 'red';
            gd.strokeRect(-100, -75, 200, 150);
            gd.restore(); //恢复:恢复到上一次保存的状态
            requestAnimationFrame(next);
        }
    }
</script>
```

![9](/assets/html5Img/canvasImg/9-3.gif "9-3")