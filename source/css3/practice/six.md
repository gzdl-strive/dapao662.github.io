---
title: CSS3动画
date: 2020-12-31 10:45:19
tag: CSS
---

>@keyframes	规定动画。	3
animation	所有动画属性的简写属性，除了 animation-play-state 属性。	3
animation-name	规定 @keyframes 动画的名称。	3
animation-duration	规定动画完成一个周期所花费的秒或毫秒。默认是 0。	3
animation-timing-function	规定动画的速度曲线。默认是 "ease"。	3
animation-delay	规定动画何时开始。默认是 0。	3
animation-iteration-count	规定动画被播放的次数。默认是 1。	3
animation-direction	规定动画是否在下一周期逆向地播放。默认是 "normal"。	3
animation-play-state	规定动画是否正在运行或暂停。默认是 "running"。	3
animation-fill-mode	规定对象动画时间之外的状态。

```html
<style>
    @keyframes gnls {
        0%{background-color: greenyellow;width: 200px;height: 200px;}
        25%{background-color: blueviolet;width: 200px;height: 200px;}
        50%{background-color: blueviolet;width: 400px;height: 200px;}
        75%{background-color: blueviolet;width: 400px;height: 300px;}
        100%{background-color: greenyellow;width: 400px;height: 300px;font-size: 25px;}
    }
    .d1{
        background-color: #cccccc;
        width:200px;
        height:200px;
        margin:10px auto;
    }
    .d1:hover {
        animation-name: gnls;
        animation-duration: 5s;
        animation-timing-function: ease-in-out;
        animation-delay: 1s;
        animation-iteration-count: 2;
        animation-direction:alternate;
    }
</style>
<body>
    <div class="d1">htmlCssjs</div>
<body>
```