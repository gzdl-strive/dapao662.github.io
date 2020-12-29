---
title: 事件
date: 2020-12-29 14:31:19
tag: HTML
---

>Canvas--不会保存图形（速度极快）
1、图形不能修改--删了重画
2、也没有事件（图形）--整个canvas有事件（canvas是一个标签）


```html
<style></style>
<body>
    <canvas id="c1" width="800" height="600"></canvas>
</body>
<script>
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');

        gd.strokeRect(100, 100, 80, 50);

        oC.onmousemove = function(ev) {
            let l = 100, t = 100, r = l + 80, b = t + 50;
            // let x=ev.clientX-oC.offsetLeft;
            // let y=ev.clientY-oC.offsetTop;
            let x = ev.offsetX;
            let y = ev.offsetY;

            gd.clearRect(0, 0, oC.width, oC.height);

            if (l <= x && x <= r && t <= y && y <= b) {
                gd.strokeStyle = 'red';
            } else {
                gd.strokeStyle = 'black';
            }
            gd.strokeRect(100, 100, 80, 50);
        }
    }
</script>
```

![5](/assets/html5Img/canvasImg/5.gif "5")