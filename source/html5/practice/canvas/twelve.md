---
title: 发给服务器
date: 2020-12-29 16:42:19
tag: HTML
---

**html页面内容**
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
    <form id='form1' action="http://localhost:8080/upload_base64" method="post" >
        <textarea name="col" rows="8" cols="80"></textarea>
    </form>
    <input type="button" id="btn" value="下载图片">
    <canvas id="c1" width="640" height="426"></canvas>
</body>
<script>
    window.onload=function () {
        let oC=document.getElementById('c1');
        let gd=oC.getContext('2d');
        let w=oC.width;
        let h=oC.height;

        let oImg=new Image();
        oImg.src='1.jpeg';
        oImg.onload=function () {
            gd.drawImage(oImg,0,0);
            let imageData=gd.getImageData(0,0,w,h);
            let data=imageData.data;
            //黄色=>扔掉蓝色（红+绿）
            //灰色=>三色平均
            for(let r=0;r<h;r++){
                for(let c=0;c<w;c++){
                    //(r*w+c)*4+0       =>  r
                    //(r*w+c)*4+1       =>  g
                    //(r*w+c)*4+2       =>  b
                    //(r*w+c)*4+3       =>  a
                    data[(r*w+c)*4+0]=data[(r*w+c)*4+1]=data[(r*w+c)*4+2]=
                        /*0.5可以调亮度**/(data[(r*w+c)*4+0]+data[(r*w+c)*4+1]+data[(r*w+c)*4+2])/3;
                }
            }
            gd.putImageData(imageData,0,0);
        }

        //
        let oBtn=document.getElementById('btn');
        oBtn.onclick=function () {
            let str=oC.toDataURL();
            document.getElementsByName('col')[0].value=str;
            document.getElementById('form1').submit();
        };
    };
</script>
```

**node服务器内容**
```js
const http = require('http');
const fs = require('fs');

http.createServer((req, res) => {
    if (req.url === "/upload_base64") {
        let arr = [];
        req.on('data', data => {
            arr.push(data);
        });
        req.on('end', () => {
            let buffer = Buffer.concat(arr);
            let filename = Math.random() + ".png";
            let buff = new Buffer(decodeURIComponent(buffer.toString().replace(/^col=/, '')).replace(/^data:[^,]+;base64,/, ''), 'base64');
            res.setHeader('Content-Disposition', 'attachment; filename=download.png');
            res.end(buff);
        })
    } else {
        fs.readFile(`www${req.url}`, (err, data) => {
            if (err) {
                res.write('404');
            } else {
                res.write(data);
            }
            res.end();
        })
    }
}).listen(8080);
```

![12](/assets/html5Img/canvasImg/12.png "12")