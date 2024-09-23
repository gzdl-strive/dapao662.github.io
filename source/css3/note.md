---
title: CSS3笔记
date: 2020-12-31 10:16:19
tag: CSS
---

### CSS3样式
* 1、圆角 border-radius
* 2、阴影 text-shadow、box-shadow
* 3、渐变 linear(线性渐变)、radial（径向渐变）     渐变其实是一种图片
* 4、rgba(red,green,blue,alpha)alpha 参数是介于 0.0（完全透明）与 1.0（完全不透明）的数字
* 5、transform
    rotate(旋转)/scale(缩放)/translate(平移)——移动端特别爱用translate/skew(倾斜)
    **transform一定要加初始值* 
* 6、transition、animation

### CSS3性能
css3样式不改变盒模型
盒模型：物体占据的空间
**css3样式（尤其是translate）不会引起重排重绘——性能更高**

### 动画
1、transition        简单、容易、方便    常用
2、animation         强大、麻烦          复杂的链式动画

### transform高级
transform：rotate(45deg) scale(2,1)先执行后面的再执行前面的
2d                  3d
rotate              rotateX/rotateY/rotateZ
translate           translateX/translateY/translateY