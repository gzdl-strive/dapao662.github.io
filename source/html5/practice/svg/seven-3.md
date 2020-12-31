---
title: raphael
date: 2020-12-31 09:46:19
tag: SVG
---

```html
<script src="https://cdn.bootcdn.net/ajax/libs/raphael/2.3.0/raphael.min.js"></script>
<script>
    window.onload=function () {
        let paper=Raphael(0,0,800,600);//位置 宽高
        let cx=400,cy=300;
        let rx=150,ry=50,rs=20;

        paper.path(`M ${cx} ${cy-ry} A ${rx} ${ry} 0 1 1 ${cx} ${cy-ry-0.001}`).attr({'stroke-width':12,'stroke':'#0000F0'});
        paper.path(`M ${cx} ${cy-ry} A ${rx} ${ry} 0 1 1 ${cx} ${cy-ry-0.001}`).attr({'stroke-width':12,'stroke':'#0000F0','transform':'r120'});
        paper.path(`M ${cx} ${cy-ry} A ${rx} ${ry} 0 1 1 ${cx} ${cy-ry-0.001}`).attr({'stroke-width':12,'stroke':'#0000F0','transform':'r-120'});
        paper.circle(cx,cy,rs).attr({'fill':'#0000F0','transform':'t-150,-50',});
    }
</script>
```

![7-3](/assets/html5Img/svgImg/7-3.png "7-3")

