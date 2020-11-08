---
title: 三角箭头
date: 2020-11-08 19:58:51
tag: css
---
### 第一步
```css
<style>
    .test1{
        height: 0;
        width: 0;
        border-top: 40px solid #00f;
        border-right: 40px solid #f00;
        border-left: 40px solid #f00;
        border-bottom: 40px solid #0f0;
    }
</style>
<div class="test1"></div>
```
![正方形](/assets/cssImg/1-3.png "正方形")
### 第二步
```css
<style>
    .test2{
        /*去掉右边框*/
        height: 0;
        width: 0;
        border-top: 40px solid #00f;
        border-left: 40px solid #f00;
        border-bottom: 40px solid #0f0;
    }
</style>
<div class="test2"></div>
```
![图](/assets/cssImg/1-4.png "图")

### 第三步
```css
/*如果想得到一个向右的箭头，是不是只要再去掉上边框和下边框就行？--》错误：当我们去掉上边框和下边框，此时盒子模型的高度是为0，无法将左边框撑高，我们将开不到任何东西，所以，不能将上边框和下边框宽度设置为0或者去掉*/
/*既不能去掉上边框也不能去掉下边框，我们可不可以隐藏上边框和下边框呢？秘密就在这儿。大家都知道css颜色有一个属性值 transparent */
<style>
    .test3{
        /*去掉右边框*/
        height: 0;
        width: 0;
        border-top: 40px solid transparent;
        border-left: 40px solid #f00;
        border-bottom: 40px solid transparent;
    }
</style>
<div class="test3"></div>
```
![success](/assets/cssImg/1-5.png "success")

### 样式
![三角箭头](/assets/cssImg/1-2.png "三角箭头")
```css
<style>
body{
    background-color: #000;
    color: #fff;
}
.arrow {
    border: 4px solid transparent;
    height: 0;
    width: 0;
    cursor: pointer;
    border-top-color: #fff;
    transform-origin: 50% 25%;
    transition: transform 2s;
}
.menu {
    width: 100px;
    font-family: sans-serif;
    height: 50px;
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
}
.arrow:hover{
    transform: rotate(180deg);
}
</style>
<div class="wrapper">
    <div class="menu">
        下拉菜单
        <div class="arrow"></div>
    </div>
</div>
```