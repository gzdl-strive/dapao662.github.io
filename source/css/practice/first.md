---
title: 居中div
date: 2020-11-08 19:45:51
tag: css
---

### 原始样式
![图一](/assets/cssImg/1-1.png "图一")
```css
<style>
body{
    background: #000;
}
.wrapper {
    width: 200px;
    height: 200px;
    background-color: red;
}
.inner {
    width: 100px;
    height: 100px;
    background-color: blue;
}
<style>

<body>
<div class="wrapper">
    <div class="inner"></div>
</div>
<body>
```

### 方法一
```css
body{
    background: #000;
}
.wrapper {
    width: 200px;
    height: 200px;
    background-color: red;
    position: relative;
}
.inner {
    width: 100px;
    height: 100px;
    background-color: blue;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

### 方法二：
```css
body{
    background: #000;
}
.wrapper {
    width: 200px;
    height: 200px;
    background-color: red;
    display: flex;
    align-items: center;
    justify-content: center;
}
.inner {
    width: 100px;
    height: 100px;
    background-color: blue;
}
```
