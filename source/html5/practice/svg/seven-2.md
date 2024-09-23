---
title: raphael
date: 2020-12-31 09:54:19
tag: SVG
---

```html
<script src="https://cdn.bootcdn.net/ajax/libs/raphael/2.3.0/raphael.min.js"></script>
<script>
    window.onload = function() {
        let paper = Raphael(0, 0, 800, 600);//位置 宽 高
        ket cx = 400, cy = 300;

        function createPath(r, ang, color) {
            let path = paper.path();
            path.attr({'stroke-width': 18, 'stroke':color})
            calc(ang, true);
            function calc(ang, isfirst = false) {
                let arr=[];
                arr.push(`M ${cx} ${cy-r}`);
                let x=cx+Math.sin(ang*Math.PI/180)*r;
                let y=cy-Math.cos(ang*Math.PI/180)*r;
                arr.push(`A ${r} ${r} 0 ${ang>180?1:0} 1 ${x} ${y}`);

                if(isfirst){
                    path.attr('path',arr.join(' '));
                }else{
                    if(ang==0){
                        path.attr('path',arr.join(' '));
                    }else{
                        path.animate({path: arr.join(' ')}, 500, 'bounce');
                    }
                }
            }
            path.calc=calc;
            return path;
        }
        let paths=[];
        function tick() {
            let oDate=new Date();
            if(paths.length==0){
                paths=[
                createPath(200,360*oDate.getHours()/12,'blue'),
                createPath(160,360*oDate.getMinutes()/60,'red'),
                createPath(120,360*oDate.getSeconds()/60,'yellow')
                ];
            }else{
                paths[0].calc(360*oDate.getHours()/12);
                paths[1].calc(360*oDate.getMinutes()/60);
                paths[2].calc(360*oDate.getSeconds()/60);
            }
        }
        tick();
        setInterval(tick,1000)
    }
</script>
```

![7-2](/assets/html5Img/svgImg/7-2.png "7-2")

