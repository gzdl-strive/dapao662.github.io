---
title: 动画
date: 2020-12-29 14:17:19
tag: HTML
---

**使用setInterval**
```html
<style>
</style>
<body>
    <canvas id="c1" width="600" height="500"></canvas>
</body>
<script>
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');
        let left = 100;
        setInterval(function() {
            gd.clearRect(0, 0, oC.width, oC.height);
            //clearRect     删除一块图形
            left += 5;
            gd.strokeRect(left, 100, 200, 150);
        }, 16)
    }
</script>
```

**requestAnimationFrame**
>`window.requestAnimationFrame()` 告诉浏览器——你希望执行一个动画，并且要求浏览器在下次重绘之前调用指定的回调函数更新动画。该方法需要传入一个回调函数作为参数，该回调函数会在浏览器下一次重绘之前执行
**注意：若你想在浏览器下次重绘之前继续更新下一帧动画，那么回调函数自身必须再次调用window.requestAnimationFrame()**

```html
<style></style>
<body>
    <canvas id="c1" width="800" height="600"></canvas>
</body>
<script>
    window.onload = function() {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');
        let left = 100;
        requestAnimationFrame(next);
        function next() {
            gd.clearRect(0, 0, oC.width, oC.height);
            left += 5;
            gd.strokeRect(left, 100, 200, 150);
            requestAnimationFrame(next);
        }
    }
</script>
```

![4](/assets/html5Img/canvasImg/4.png "4")