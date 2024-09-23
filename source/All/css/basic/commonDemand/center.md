---
title: 水平垂直居中
date: 2021-03-30 10:17:20
tog: css
toc: true
---

让元素在父元素中呈现出水平垂直居中的形态，无非就 2 种情况：
* 单行的文本、inline 或者 inline-block 元素；
* 固定宽高的块级盒子；
* 不固定宽高的块级盒子；

### 单行的文本、inline 或 inline-block 元素
#### 水平居中
此类元素需要水平居中，则父级元素必须是块级元素(block level)，且父级元素上需要这样设置样式：
```css
.parent {
    text-align: center;
}
```

#### 垂直居中
**方法一：通过设置上下内间距一致达到垂直居中的效果**
```css
.single-line {
    padding-top: 10px;
    padding-bottom: 10px;
}
```
**方法二：通过设置height和line-height一致达到垂直居中**
```css
.single-line {
    height: 100px;
    line-height: 100px;
}
```

### 固定宽高的块级盒子
#### 方法一：absolute+ + 负margin
![32](/assets/cssImg/allBasic/32.png)
#### 方法二：absolute + margin auto
![33](/assets/cssImg/allBasic/33.png)
#### 方法三：absolute + calc
![34](/assets/cssImg/allBasic/34.png)

### 不固定宽高的块级盒子
#### 方法一：absolute + transform
![35](/assets/cssImg/allBasic/35.png)
#### 方法二：line-height + vertical-align
![36](/assets/cssImg/allBasic/36.png)
#### 方法三：writing-mode
![37](/assets/cssImg/allBasic/37.png)
#### 方法四：table-cell
![38](/assets/cssImg/allBasic/38.png)
#### 方法五: flex
![39](/assets/cssImg/allBasic/39.png)
#### 方法六：grid
![40](/assets/cssImg/allBasic/40.png)


