---
title: 聚光灯效果
date: 2020-11-07 11:51:36
tag: sass
---

![聚光灯](/assets/sassImg/聚光灯.gif "聚光灯")

## index.scss

```scss
$primary-color: #222222;
$font-color: #333333;
* {
    margin: 0;
    padding: 0;
}
body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: $primary-color;
}
h1 {
    position: relative;
    /*文字大写*/
    text-transform: uppercase;
    color: $font-color;
    /*1rem = 16px*/
    font-size: 8rem;
    &::after{
        content: 'csgo-rush-b';
        position: absolute;
        top: 0;
        left: 0;
        color: transparent;
        background-image: linear-gradient(to right, #c23616,
         #192a56, #00d2d3,yellow, #6d214F,#2e86de,
          #4cd137, #e84118);
          /*背景绘制区域 当值为text的时候 
          代表设置了文字的镂空效果 但是前提必须是文字颜色为透明色*/
        background-clip: text;
        /*谷歌浏览器私有属性*/
        -webkit-background-clip: text;
        /*利用裁剪来创建元素的可显示区域 circle表示创建了圆形
        100px 表示圆形的直径，0%和50%表示圆形的圆心 
        利用at关键字隔开圆形的直径和圆形的圆心*/
        clip-path: circle(100px at 0% 50%);
        /*动画： 名称 时长 infinite表示无限次播放*/
        animation: move 5s infinite;
    }
    /*下面设置圆心移动效果*/
}
@keyframes move {
    0% {
        clip-path: circle(100px at 0% 50%);
    }
    50% {
        clip-path: circle(100px at 100% 50%);
    }
    100% {
        clip-path: circle(100px at 0% 50%);
    }
}
```