---
title: 文字
date: 2020-12-29 15:35:19
tag: HTML
---

>文字
strokeText()  gd.fillText('你好',100,100);100，100是基线位置（左下角）
fillText
gd.font='40px 楷体';

```html
<style>
    body{
        background-color: black;
        text-align: center;
    }
    #c1{
        background-color: #ccc;
    }
</style>
<body>
    <canvas id="c1" width="800" height="600"></canvas>
</body>
<script>
    window.onload=function () {
        let oC = document.getElementById('c1');
        let gd = oC.getContext('2d');
        gd.font = 'bold 40px 楷体';
        gd.fillStyle = 'red';
        gd.fillText('你好',100,100);
    }
</script>
```

![8](/assets/html5Img/canvasImg/8.png "8")