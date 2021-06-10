---
title: CSS盒模型
date: 2021-05-26 11:00:20
tog: css
toc: true
---

### 引言 
>CSS盒模型本质上是一个盒子，盒子包裹着HTML元素，盒子由四个属性组成，从内到外分别是：content 内容、padding 内填充、border 边框、外边距 margin

### 盒模型的分类
* W3C盒子模型(标准盒模型)
![w3c](/assets/cssImg/w3cBox.jpg "w3c盒模型")
* IE盒子模型(怪异盒模型)
![ie](/assets/cssImg/ieBox.jpg "ie盒模型")

### 两种盒模型的区别
**宽度和高度计算方式不同**
标准盒模型：
>`width = content-width(内容的宽度)`
>`height = content-height(内容的高度)`

怪异盒模型:
>`width = content-width + padding-width + border-width`
>`height = content-height + padding-height + border-height`

### 如何在CSS中设置这两个模型
标准盒模型:
```css
xxx {
    box-sizing: content-box;
}
```
怪异盒模型：
```css
xxx {
    box-sizing: border-box;
}
```

### 外边距合并
所谓外边距合并，指的是margin 合并，MDN是这样定义的
>块的顶部外边距和底部外边距有时被组合(折叠)为单个外边距，其大小是组合到其中的最大外边距，这种行为称为外边距合并。

要注意的是，外边距合并只针对块级元素，而且是顶部或底部的外边距

外边距合并有下面几种情况
* 相邻兄弟元素
```html
<div class="up">我在上面</div>
<div class="down">我在下面</div>
```
```css
.up {
  width: 100px;
  height: 100px;
  border: 1px solid blue;
  margin: 100px;
}
.down {
  width: 100px;
  height: 100px;
  border: 1px solid red;
  margin: 100px;
}
```
![外边距合并](/assets/cssImg/margin.jpg "外边距合并")
* 父子元素
如果在块级父元素中，不存在上边框、上内补、inline content、清除浮动这四个属性，(对于不存在上边框和上内补，也可以理解为上边框和上内补宽度为0)，那么这个块级元素和其第一个子元素的存在外边距合并，也就是上边距”挨到一起“，那么此时父元素展现出来的外边距，将会是子元素的margin-top 和父元素的margin-top 的较大值。
```html
<div class="parent">
  <div class="child">我是儿子</div>
</div>
```
```css
.parent {
  width: 100px;
  height: 200px;
  background: red;
  margin-top: 50px;
}
.child {
  width: 50px;
  height: 50px;
  margin-top: 100px;
  border: 1px solid blue;
}
```
![外边距合并2](/assets/cssImg/margin2.jpg "外边距合并")

### BFC
>一个块格式化上下文（block formatting context）是Web页面的可视化CSS渲染的一部分。它是块盒子的布局发生，浮动互相交互的区域。

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**

通俗一点来讲，可以把 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

**触发 BFC：**
* body 根元素
* 浮动元素：float 除 none 以外的值
* 绝对定位元素：position (absolute、fixed)
* display 为 inline-block、table-cells、flex
* overflow 除了 visible 以外的值 (hidden、auto、scroll)

**注意根元素就创建了个BFC**

**那么BFC 又有以下特点：**
* 内部块级盒子垂直方向排列
* 盒子垂直距离由margin 决定，同一个BFC 盒子的外边距会重叠
* BFC 就是一个隔离的容器，内部子元素不会影响到外部元素
* BFC 的区域不会与float box叠加
* 计算BFC高度的时候，浮动元素也会参与计算
* 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。

**BFC 的用途：**
* 清除浮动
* 解决外边距合并
* 布局

**看到这里相信大家都知道怎么解决外边距重合了，答案是: 将两个外边距重合的元素放在不同的BFC 容器中**

看看上面的例子怎么解决外边距重合
* 相邻兄弟元素
```html
<div class="up">我在上面</div>
<div class="down">我在下面</div>
```
```css
.up {
  width: 100px;
  height: 100px;
  border: 1px solid blue;
  margin: 100px;
}
.down {
  width: 100px;
  height: 100px;
  border: 1px solid red;
  margin: 100px;
  display: inline-block; /*触发BFC*/
}
/* up和down两个元素处于不同的BFC ，外边距不重合 */
```
* 父子元素
```html
<div class="parent">
  <div class="child">我是儿子</div>
</div>
```
```css
.parent {
  width: 100px;
  height: 200px;
  background: red;
  margin-top: 50px;
  overflow: hidden; /*触发父元素BFC,取消上边距合并*/
}
.child {
  width: 50px;
  height: 50px;
  margin-top: 100px;
  border: 1px solid blue;
}
```

### IFC
既然块级元素会触发BFC，那么内联元素会触发的则是IFC
IFC 只有在一个块元素中**仅包含**内联级别元素时才会生成
![ifc](/assets/cssImg/ifc.jpg "ifc")

**IFC原理**
* 内部的box 会在水平方向排布
* 这些box 之间的水平方向的`margin,border,padding` 都有效
* Box垂直对齐方式：以它们的底部、顶部对齐，或以它们里面的文本的基线（baseline）对齐（默认，文本与图片对其），例：line-heigth与vertical-align。

### BFC实例
**实例1：相邻兄弟元素外边距重叠-》解决方法：使得它们处于不同BFC中**
```html
<section id="margin">
    <style>
        * {
            padding: 0;
            margin: 0;
        }
        #margin {
            background: pink;
            overflow: hidden;
        }
        p {
            margin: 15px auto 25px;
            background: red;
        }
    </style>
    <p>1</p>
    <div style="overflow: hidden">
        <p>2</p>
    </div>
    <p>3</p>
    <p>4</p>
</section>
```
请看这里的第二个p元素`<p>2</p>`他被一个父元素包裹，并且父元素有 `overflow:hidden `样式，前面的如何创建BFC的第一条就说了 `overflow:hidden` 可以创建一个BFC。结果如下图所示。
![bfc1](/assets/cssImg/bfc1.png "BFC")
我们看这里的2，它的上下外边距并没有与1和3发生重叠，但3和4外边距发生了重叠。就解释了BFC创建了一个独立的环境，这个环境中的元素不会影响到其他环境中的布局，所以BFC内的外边距不与外部的外边距发生重叠。

**实例2：浮动元素叠在正常元素上**
```html
<section id="layout">
    <style media="screen">
        #layout {
            background: red;
        }
        #layout .left {
            float: left;
            width: 100px;
            height: 100px;
            background: pink;
        }
        #layout .right {
            height: 110px;
            background: #ccc;
        }
    </style>
    <div class="left"></div>
    <div class="right"></div>
</section>
```
效果如下：
![bfc2](/assets/cssImg/bfc2.png "BFC")
这里其实是浮动元素叠在 .right 元素的上面，如果我们想让.right元素不会延伸到 float元素怎么办,其实我们在.right元素上加 `overflow:hidden` （用其他的方式创建BFC也可以）创建BFC就可以解决。**因为BFC不会与浮动元素发生重叠。**
![bfc3](/assets/cssImg/bfc3.png "BFC")

还有一种情况很常见，就是由于子元素浮动，导致父元素的高度不会把浮动元素算在内，那么我们在父元素创建BFC就可以让可以让浮动元素也参与高度计算。



