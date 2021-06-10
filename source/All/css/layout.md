---
title: 圣杯布局和双飞翼布局
date: 2021-06-02 17:32:20
toc: true
---

> 这两种三列布局的效果是一样的，实现的效果如上图所示，其特点为：
>
> - header 和 footer 的宽度为整个浏览器的宽度
> - 中间是各三列布局，left 和 right 的宽度是固定的
> - 而 middle 的内容的宽度是自适应占满中间位置的

**其实圣杯布局和双飞翼布局在前面的处理都是一样的，只不过在中间处理的时候有点差异**

## 前期处理

- 为了避免出现问题我们先给 body 设置一个 min-width:600px;
- 再将 header 和 footer 的宽度设为 100%（整个浏览器的页面），并添加背景色便于区分。
- 在 content 中我们给 left，right，midlle 都添加 float:left 效果，设置 left，right（宽 200px, 高 300px）的宽高。
- middle 的宽度设置为 100%，高度为与 left，right 的高度相同，同样为了便于看效果我们这里也给它们一个背景色。
- 这里有个需要注意的地方，我们还应该给 content 设置一个高度，content 中的 div 我们添加了 float：left 属性，因此他们都脱离了文本流，导致出现了高度塌陷（给父元素设置 BFC 来解决，因为 BFC 计算高度的时候，浮动元素也参与计算）。

```css
body {
  margin: 0;
  padding: 0;
  min-width: 600px;
}
header, footer {
  height: 100px;
  width: 100%;
  background-color: #666;
}
.content {
  overflow: auto; /*设置BFC解决高度塌陷问题*/
}
.left, .right, .middle {
  float: left;
}
.left, .right {
  width: 200px;
  height: 300px;
  background: pink;
}
.middle {
  height: 300px;
  width: 100%;
  background-color: yellowgreen;
}
```
![1](/assets/cssImg/layout.png "1")
我们发现现在的页面是这样子的，那这是怎么一回事呢？
答案就是，left,right,middle本来是在一条直线上的，但是content宽度不够，就把left和right两个盒子挤下来了。

那么我们现在来看看者两种布局，试着解决这个问题吧:

## 圣杯布局
* 我们先给content设置一个`padding: 0 200px`,将左右两边各腾出200px宽度。
* 接下来我们只需要把left和right两个盒子放在这个位置就可以了
* 最后分别给left,right设置margin属性调整位置。
```css
.content {
  overflow: auto;
  padding: 0 200px;
}
.left {
  margin-left: -100%;
}
.right: {
  margin-left: -200px;
}
```
我们再来看一下效果
![2](/assets/cssImg/layout2.png "2")
现在就可以看到left，right和middle就来到了同一行啦，接下来的操作就So easy啦，给left和right分别一个relative定位将他们调整到两端即可。
```css
.left {
  margin-left: -100%;
  position: relative;
  left: -200px;
}
.right {
  margin-right: -200px;
  position: relative;
  left: 200px;
}
```
完事,效果如下：
![3](/assets/cssImg/layout3.png "3")

## 双飞翼布局
那我们看看双飞翼布局是怎么解决这个问题的呢。来往下看
* 双飞翼布局是在middle下添加了一个middle-inner盒子，将middle的内容放在这个盒子里面（这有什么用呢？不着急咱先往下看）。
* 我们和圣杯布局中一样也给float，left设置margin值调整位置。
* 但是此布局就不需要给left,right设置定位啦。
```css
#left{
    float: left;
    margin-left: -100%;
}
#right{
    float: left;
    margin-left: -200px;
}
```
![4](/assets/cssImg/layout4.png "4")
这时我们发现middle中的内容不见了,此时我们之前在midlle里面添加的midlle-inner盒子就派上用场啦，此时只需要给这个盒子设置一个margin：0 200px,那么我们的内容是不是来到中间展示了呢。
```css
.middle-inner{
    margin: 0 200px;
}
```
看效果
![5](/assets/cssImg/layout5.png "5")

## 总结
最后我们来总结一下，双飞翼布局其实和圣杯布局的精髓是一样的，都是通过设置负margin来实现元素的排布

* 不同的就是html结构，双飞翼是在middle元素内部又设置了一个milddle-inner并设置它的左右margin，而非圣杯布局的padding，来排除两边元素的覆盖。
* 双飞翼布局可以多了一个html结构，但是可以不用设置left,right元素的定位。

### 圣杯布局代码
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="wrap">
        <div id="header">header</div>
        <div id="content">
            <div id="middle">
                middle
            </div>
            <div id="left">left</div>         
            <div id="right">right</div>
        </div>
        <div id="footer">footer</div>
    </div>
</body>
</html>
```
```css
*{
    margin: 0;
    padding: 0;
}
body{
    min-width: 600px;
}
#header,#footer{
    height: 50px;
    width: 100%;
    background: grey;
}
#middle,#left,#right{
    float: left;
}
#content{
    overflow: hidden;
    padding: 0 200px;
}
#left,#right{
    width: 200px;
    height: 200px;
    background: pink;
}
#middle{
    width: 100%;
    height: 200px;
    background: yellowgreen; 
}
 #left{
    margin-left:-100% ;
    position: relative;
    left:-200px;
} 
#right{
    margin-left: -200px;
    position: relative;
    left:200px; 
}
```

### 双飞翼布局
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="wrap">
        <div id="header">header</div>
        <div id="content">
            <div id="middle">
                <div class="middle-inner">
                    middle
                </div>
            </div>
            <div id="left">left</div>         
            <div id="right">right</div>
        </div>
        <div id="footer">footer</div>
    </div>
</body>
</html>

```
```css
*{
    margin: 0;
    padding: 0;
}
.wrap{
    min-width: 600px;
}
#header,#footer{
    height: 50px;
    width: 100%;
    background:grey;
}
#left,#right{
    width: 200px;
    height: 200px;
    background: green;
}

#middle{
    background: pink;
    width: 100%;
    float: left;
    height:200px;
}
#content{
    overflow: hidden;
}
#left{
    float: left;
    margin-left: -100%;
}
#right{
    float: left;
    margin-left: -200px;
}
.middle-inner{
    margin: 0 200px;
}

```
