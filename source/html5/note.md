---
title: HTML5笔记
date: 2020-12-29 10:15:19
tag: HTML
toc: true
---

>HTML5:
1、geolocation--定位
2、video、audio
3、localStorage
    类似cookie：   小(4k)-出现时间早、浏览器和服务器共享
    localStorage    大（5M）、浏览器独享
4、*WebWorker
    多进程
5、WebSQL、IndexedDB--安全隐患（几乎用不着）
    *W3C删除了
6、文件操作、文件拖拽
7、canvas--画图
    SVG/VML
8、manifest文件        前台控制缓存
    *本地应用(用处不大)

## geolocation--定位
1、原理
    PC端:IP地址    精度非常差
    移动:GPS      精度很高
2、PC端
    IP地址--IP库
    Chorme->找google.com要IP库
    IE->mircosoft.com
----

`getCurrentPosition`      获取位置（一次）

`watchPosition`           不断获取位置
clearWatch

-----
coords.latitude	十进制数的纬度
coords.longitude	十进制数的经度
coords.accuracy	位置精度
coords.altitude	海拔，海平面以上以米计
coords.altitudeAccuracy	位置的海拔精度
coords.heading	方向，从正北开始以度计
coords.speed	速度，以米/每秒计
timestamp	响应的日期/时间

**练习**
```js
// html代码：<p id="p1"></p> <button onclick="geolocation()">点击</button>
const x = document.getElementById('p1');
function geolocation() {
    if(navigator.geolocation){
        //getCurrentPosition（成功，失败，参数）
        navigator.geolocation.getCurrentPosition(function (res) {
            console.log('ok');
            alert('成功') ;
        },function (err) {
            console.log('fail');
            alert('失败');
        });
    }else{
        x.innerHTML='这个浏览器不支持Geolocation定位';
    }
}
```



## localStorage: 5M/域名
>1、域名，跨域
2、极其方便
3、用途:记录用户名、保存草稿
localStorage    永久储存
sessionStorage  会话期间存储--浏览器一关就没了

**练习1**
```js
localStorage.a = 12; //存
alert(localStorage.a); //取
localStorage.b=24;
//遍历
for(let name in localStorage){
    alert(`${name}: ${localStorage[name]}`);
}
//删除
delete localStorage.b;
```
**练习2-->保存草稿**
```html
<textarea row="20" cols="50" id="txt"></textarea>
<input type="button" value="发送" id="btn"/>
```
```js
window.onload = function() {
    let txt = document.getElementById('txt');
    let btn = document.getElementById('btn');

    txt.value = localStorage.temp_txt || '';
    txt.oninput = function() {
        localStorage.temp_txt = txt.value;
    }
    btn.click = function() {
        alert('发送成功');
        delete localStorage.temp_txt;
    }
}
```

## webWorker-web多进程
>主进程--UI进程
子进程--工作进程
webWorker--工作进程
1、不能控制UI的东西，可以数据交互
2、子进程不能再创建子进程
3、跨域
多进程--更充分发挥计算机资源(不是内存，不是IO,不是网络，而是CPU)

**练习**
```js
// webworker.html
let ow=new Worker('1.js');
ow.onmessage=function(ev){
    alert(ev.data);
}
ow.postMessage({n1:66,n2:33});
```
```js
//1.js
this.onmessage=function (ev) {
    let {n1,n2}=ev.data;
    let result=n1+n2;
    this.postMessage(result);
}
```

## 画图
>canvas  位图--放大失真    html5标准
SVG     矢量图--无限缩放   不是html5的东西，是一个独立标准
VML     矢量图             IE的矢量图

### canvas
>1、路径操作：
    相当于选取-没有效果,还需要后续操作
    moveTo
    lineTo
2、边线：stroke()
3、填充：fill（）
线宽：lineWidth
线色，填充颜色:strokeStyle,fillStyle
一切路径操作之前，一定要**beginPath()**
----
`beginPath()`         **清楚之前路径**      **必须加**
`closePath()`         **闭合之前路径**
----
Canvas怎么自适应->`window.onresize`
----
**矩形：**
rect()          路径操作
strokeRect()    图形操作
fillRect()      图形操作
clearRect       删除一块图形
Canvas--不会保存图形（速度极快）
1、图形不能修改--删了重画
2、也没有事件（图形）--整个canvas有事件（canvas是一个标签）
1000/16=62.5(人体极限60帧)
----
Canvas动画--擦了重画：
1、全擦-重画
2、动画-requestAnimationFrame函数
---
线：moveTO/lineTo
矩形：rect
圆：arc(弧)        路径操作
---
**文字**
strokeText()  gd.fillText('你好',100,100);100，100是基线位置（左下角）
fillText
gd.font='40px 楷体';
---
canvas也有transform
1、rotate
2、translate
3、scale
---
**注意**
>canvas--建议必加
1、beginPath
2、save,restore
---
canvas:
1、路径操作
2、beginPath、save/restore
3、形状:线、弧、矩形
4、变换：canvas左上角
---
canvas--游戏、图表

canvas:
1、图片操作--90%
    drawImage(oImg,x,y)--整张图片
    oImg对象:<img>、Image对象、另一个canvas、另一个video、base64(一种编码方式)
    drawImage(
        oImg
        sx,sy,sw,sh         source
        dx,dy,dw,dh         dest
    )
    停止、暂停、移动...
2、像素级操作
    **性能极低**
    获取一块像素      =>  "数组"
    设置一块像素      =>  "数组"->Canvas
    创建一块像素      =>  "空白数组"

    1像素占4位:rgba
    r:  0~255
    g:  0~255
    b:  0~255
    a:  0~255

    w:200 h:300
    0行0列        0
    0行1列        1
    0行55列       55
    1行0列        200
    2行0列        400
    r行c列        r*w+c

3、video和canvas配合
    视频滤镜

4、图形后续处理
    用户打开、下载、存到服务器
canvassend.html 1.jepg server.js
---
canvas 位图-缩放失真； 不保存图形-不能修改、没有事件； 性能非常高
        适合:游戏、大型图表

SVG    矢量图-缩放不失真； 保留图形-能修改、有事件；   性能一般（跟普通标签一样）
        适合：交互频繁--普通图表

### SVG
>1、矢量
2、保存-事件、属性
3、性能一般
SVG虽然是标签，不是HTML标准--SVG标准
---
SVG标签：
    只能做属性--决定图形形状
    可以放样式（style）--视觉上效果-颜色...
    *放样式里
    **属性的样式优先级太低了

    属性<*<标签<class<ID<行间

1、SVG由标签组成-svg、line、...
2、属性：形状的参数
    样式：视觉
3、事件-修改
    添加事件    --一样
    修改样式    --一样
    属性操作    --.setAttribute,.getAttribute

矢量图都没有单位
---
SVG和HTML--兄弟（基于XML,SVG更像XML）
---
图形：
```html
<line x1 y1 x2 y2>
<rect x y width height rx ry>
<circle cx cy r>
<ellipse cx cy rx ry>
```
样式：
stroke  边线颜色
stroke-width    线宽
fill        填充

---
M = moveto
L = lineto
H = horizontal lineto
V = vertical lineto
C = curveto
S = smooth curveto
Q = quadratic Belzier curve
T = smooth quadratic Belzier curveto
A = elliptical Arc
Z = closepath
<path>
M   moveTo  x y
L   lineTo  (x y)+
A   arc     rx ry x旋转 大弧标志(大弧：1,小弧0) 镜像标志（起点终点连线，左：0，右：1）  终点x y
---
SVG:
1、标签:
```html
<svg><line><circle><path>
```
2、样式:style
3、属性:JS操作--getAttribute/set
4、事件
5、css3样式--不认
    动画
---
JS生成元素
document.createElement  创建HTML元素

NS-->Name Space(document.createElementNS('http://www.w3.org/2000/svg','line'))

document.createElement=>document.createElementNS("http://www.w3.org/1999/xhtml")

如果要创建svg标签也要用NS
----
矢量图-用库

Raphael 强
---
echarts     简单、方便--图表
d3          复杂、强大
---
## CSS3样式
>1、圆角 border-radius
2、阴影 text-shadow、box-shadow
3、渐变 linear(线性渐变)、radial（径向渐变）     渐变其实是一种图片
4、rgba(red,green,blue,alpha)alpha 参数是介于 0.0（完全透明）与 1.0（完全不透明）的数字
5、transform
    rotate(旋转)/scale(缩放)/translate(平移)——移动端特别爱用translate/skew(倾斜)
    **transform一定要加初始值
6、transition、animation

### Css3性能：
>css3样式不改变盒模型
盒模型：物体占据的空间
css3样式（尤其是translate）不会引起重排重绘——性能更高
----
DOM操作——（为什么耗费时间）——越大越慢
1、重排（）
2、重绘
----
动画：
1、transition        简单、容易、方便    常用
2、animation         强大、麻烦          复杂的链式动画
----
transform高级
transform：rotate(45deg) scale(2,1)**先执行后面的再执行前面的**
2d                  3d
rotate              rotateX/rotateY/rotateZ
translate           translateX/translateY/translateY
----
移动端——布局、touch、库
图形——canvas、SVG、echars、d3
----
3d transform--perspective、preserver-3d
1、perspective--自己       **视觉效果**
    *只给最外层加一次*
2、preserver-3d--子元素     **子元素可以脱离父级**
    *每个需要子级出来的地方*