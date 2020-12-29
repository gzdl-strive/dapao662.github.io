---
title: 转换4
date: 2020-12-29 16:03:19
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

        let g1 = {l: 100, t: 100, w: 200, h: 150, r: 0};
        let g2 = {l: 200, t: 100, w: 300, h: 100, r: 0};
        let a = 1;
        let b = -1;
        requestAnimationFrame(next);
        function next() {
            gd.clearRect(0, 0, oC.width, oC.height);
            close(g1, a);
            close(g2, b);
            requestAnimationFrame(next);
        }
        function close(g, x) {
            g.r + = x;
            gd.save(); //保存canvas的状态（图形不保存）————颜色、translate\rotate
            gd.translate(g.l + g.w / 2, g.t + g.h / 2);//会迭代之前的位置
            gd.rotate(g.r * Math.PI / 180);
            gd.strokeStyle = 'red';
            gd.strokeRect(-g.w / 2, -g.h / 2, g.w, g.h);
            gd.restore();//恢复：恢复到上一次保存的状态
        }
    }
</script>
```

![9-4](/assets/html5Img/canvasImg/9-4.gif "9-4")