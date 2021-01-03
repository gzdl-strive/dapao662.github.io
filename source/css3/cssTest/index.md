---
title: CSS
date: 2021-01-03 12:36:19
tag: CSS
---

>锚点定位
```html
<a href="#open"></a> ... <h1 id="open"></h1>
```
>base标签 可以给页面所有a标签添加属性 在head标签内 `<base target="_blank"></base>`
链接伪类选择器: `a:link,a:hover,a:active,a:visited`

### 结构伪类选择器`(CSS3) `
>`:first-child` 匹配其父元素的第一个子元素
`:last-child` 匹配其父元素的最后一个子元素
`:nth-child(n)` 匹配其父元素的第n个子元素
`:nth-child(even)`选择偶数子元素,`:nth-child(odd)`选择奇数子元素
`:nth-child(n)`如果直接写的是n会从0开始选择全部
`:nth-child(2n)`选择偶数
`:last-nth-child(n)` 逆序的`nth:child`

>`:target目标伪类选择器`:选择器可用于选取当前活动的目标元素
`text-shadow` 水平位置 垂直位置 模糊距离 阴影颜色
交集选择器`div.red p.green`
并集选择器`span, div, a {}`
后代选择器 `div p (空格分隔，div下的p)(可以选择儿子 孙子 重孙子...)`
子元素选择器 `.demo>h1--只选择子元素不会选中孙子...`
属性选择器
    `a[title]`属性选择器使用中括号,选中带有title属性, 
    `input[type=text] `选中input含有type="text"属性的
    `div[class^=font]`选中div中含有以font开头的class属性的元素
    `div[class$=footer]`选中div中含有以footer结尾的class属性的元素
    `div[class*=content]`选中div中含有content的class属性的元素--任意位置
伪元素选择器
`E::first-letter`文本的第一个单词或字
`E::first-line`文本第一行
`E::selection`可改变选中文本的样式
`E::before`在E元素内部开始位置创建一个元素，该元素为行内元素，且必须要结合content属性使用
`E::after`在E元素内部结束位置创建一个元素，该元素为行内元素，且必须要结合content属性使用

### 背景
>`background-size`：设置背景图片设置尺寸:尽量只改一个值，防止缩放失真扭曲
`background-size: cover;` 会自动缩放比例，保证图片始终填满背景区域，如有溢出部分会隐藏
`background-size: contain;` 会自动调整比例，保证图片始终完整显示在背景区域
----
`多背景CSS3` :以逗号分隔可以设置多背景,可用于自适应布局
```css
background-image: url('img/1.png') no-repeat left top, url('img/2.png') no-repeat right bottom
```
一个元素可以设置多重背景图像，每组属性间使用逗号分隔，如果多重背景存在交集，前面的背景图会覆盖在后面的背景图之上
为了避免背景色将图像盖住，背景色通常都定义在最后一组上
```css
background:url() ..., url() ..., url() , #f00;
```
### 小技巧
**在一行内的盒子内，我们设定行高等于盒子的高度，就可以使文字垂直居中**
**如果设置了行高就可以不用设置高度，行高会撑开**

## css三大特性：层叠性，继承性，优先级
### 层叠性：浏览器处理冲突的一个能力
>如果样式冲突，遵循的原则是就近原则,哪个样式离结构近，就执行那个样式.如果样式不冲突，不会层叠

### 继承性
>子标签会继承父标签的某些样式,如颜色和字号.(text-, font-, line-这些元素开头都可以继承，以及color属性)

### 优先级
>`css特殊性(Specificity)`:关于`css权重`，用一个四位数字表示(css2是三位),更像4个级别，值从左往右，左面的最大，数位之间没有进制，级别之间不可超越
   * 0, 0, 0, 0
   * 继承或*的贡献值为     0, 0, 0, 0
   * 每个元素(标签)贡献值为 0, 0, 0, 1
   * 每个类，伪类贡献值为   0, 0, 1, 0
   * 每个id贡献值为        0, 1, 0, 0
   * 每个行内样式贡献值为   1, 0, 0, 0
   * 每个!important贡献值为 ∞无穷大
   **权重叠加**
    * .nav ul li --> 0, 0, 1, 2
    * a:hover    --> 0, 0, 1, 1
    * #nav ul li --> 0, 1, 0, 2  

--------

* 表格边框合并 `border-collapse: collapse`：合并相邻边框
* 背景图片更改大小只能用`background-size`
* 背景图片更改位置用`background-position`
* 清除默认内外边距`* { margin: 0; padding: 0 }`
* 行内元素只有左右外边距，没有上下外边距
* 注意：尽量不给行内元素指定上下内外边距        
* 相邻块元素垂直外边距合并（也称外边距塌陷）解决方法：尽量避免 

**嵌套块元素外边距合并问题**
* 解决方法1：给父元素加一个边框
* 解决方法2：给父元素添加overflow:hidden

* 外盒（空间）尺寸: `width + padding + margin + border`
* 内盒（实际）尺寸:` width + padding + border`

**如果盒模型加了width/height,padding会撑开盒子宽高,解决方法：不加宽/高。继承父元素的宽也不会撑开**

**盒子模型布局稳定性：`width > padding > margin`**
* margin会有外边距合并，还有ie6下面margin加倍的bug
* padding会影响盒子大小，需要进行加减计算(麻烦),其次使用
* width没有问题，我们经常使用宽度剩余法，高度剩余法来做

**css3中使用`box-sizing`来指定盒模型**
box-sizing:以前的盒模型(默认) padding会撑开盒模型
box-sizing: border-box; //padding, border不撑开盒子--》盒子大小就是宽度，就是padding和border是包含到width里面的

**box-shadow：水平位置 垂直位置 模糊距离 阴影尺寸（影子大小） 阴影颜色 内/外阴影**

## 浮动
### 浮动详细内幕特性
>浮动脱离标准流，不占位置，会影响标准流。浮动只有左右浮动
浮动首先创建包含块的概念（包裹），浮动的元素总是找离他最近的父级元素对齐。但是不会超出内边距的范围。
一个父盒子里的子盒子，如果其中一个子元素有浮动，其他子元素都要浮动才能一行对齐
如果块级元素添加了浮动，那么具有行内块的特性
如果行内元素添加了浮动，那么具有行内块的特性
**特别注意：首先浮动的盒子需要和标准流的父级搭配使用，其次特别注意浮动可以是元素显示模式体现为行内块特性

### 清除浮动本质
>清除浮动本质：主要为了解决父级元素因为子级浮动引起内部高度为0的问题
* 正常标准流盒子：子盒子是标准流，父盒子虽然没有高度，但是会撑开父盒子高度
* 子盒子浮动：子盒子浮动，脱离标准流，父盒子没有高度就为0，不会被撑开盒子

>清除浮动方法
* 1、额外标签法，在浮动元素末尾添加一个空标签，例如`<div style="clear: both"></div>`,或者其他标签br等亦可。**缺点：添加了许多无意义的标签，结构化较差**
* 2、父级添加`overflow:hidden/auto/scroll方法`-》通过触发`BFC`的方式，可以实现清除浮动效果。**优点：代码简洁** 。**缺点：内容增多时候容易造成不会自动换行导致内容被隐藏调，无法显示需要溢出的元素**
* 3、是由after伪元素清除浮动
:after是空标签的升级版，好处是不用单独添加标签
```css
.clearfix:after {
    content: '.';   /*内容为. ,尽量加不要空，否则旧版本浏览器有空隙*/
    display: block; /*转换为块级元素*/
    height: 0; /*高度为0*/
    visibility: hidden; /*隐藏盒子*/
    clear: both; /*清除浮动*/
}
/* ie6，7处理的方式 */
.clearfix {
    *zoom: 1; 
    /* *代表ie6、7能识别的特殊符号,待遇这个*的属性，只有ie6,7才执行
    zoom就是ie6,7清除浮动的方法 */
}
```
* 4、双伪元素清除浮动
```css
.clearfix:before, .clearfix:after {
    content: "";
    display: table;/*触发bfc防止外边距合并*/
}
.clearfix:after {
    clear: both;
}
.clearfix {
    *zoom: 1;
}
```

## 定位：边偏移和定位模式
>边偏移：`left/right/top/bottom`
定位模式：position: static（自动定位,默认）/relative(相对定位-相对于原文档流位置进行定位)/ absolute（绝对定位-相对于其上一个已经定位的父元素进行定位）/fixed（固定定位-相对于浏览器窗口进行定位）

relative:相对定位--》相对定位的盒子仍在标准流中，他后面的盒子仍以标准流的方式对待他(相对定位不脱标)
absolute:绝对定位--》绝对定位完全脱离标准流，它不占有位置
    1、如果父元素没有定位，会以浏览器为准对齐(document)
    2、如果父元素有定位，会以父元素为准对齐
子绝父相的由来（父元素如果加绝对定位，那么就不占有位置脱离标准流）：
    1、相对定位： 占有位置 不脱标
    2、绝对定位： 不占有位置 完全脱标
**定位模式转换**
跟浮动一样，当元素添加了绝对定位/固定定位后，元素模式发生转换，都变成行内块模式

------
>外轮廓线 outline:none
```css
input {
    outline: none;
}
```
>防止拖拽文本域 resize： none

> 垂直居中:
`vertical-align: baseline|top|middle|bottom` :对块级元素居中不管用,使得图片或表单这些行内块元素对齐
baseline：默认基线对齐
middle: 中线对齐
**应用： 去除图片底侧空白缝隙**
图片或表单等行内块元素，它的底线会和父级盒子基线对齐。这样会造成一个问题，就是底部会有一个空白缝隙 
解决方法：1、转换为块级元素2、使用vertical-align: middle|top

### 溢出文字隐藏 
word-break: break-all;允许单词拆开显示
            keep-all;保持完整性,连字符特殊（-）
white-space: 设置或检索对象内文本显示方式
    normal:默认处理方式
    nowrap: 强制在同一行显示所有文本，直到文本结束或遭遇br标签对象才换行
text-overflow：文字溢出
    clip 不显示省略标记(...),而是简单的裁切
    ellipsis: 当对象内文本溢出时显示省略标记(...)
```html
<div style="width: 120px; height: 20px; border: 1px solid #f00; word-break: keep-all;">my name is ze-ro</div>
<div style="width: 120px; height: 20px; border: 1px solid #f00; white-space: nowrap;">我的名字是索隆，是个剑客</div>
<div style="width: 120px; height: 20px; border: 1px solid #f00; text-overflow: ellipsis; white-space: nowrap;overflow: hidden;">我的名字是索隆，是个剑客</div>
```

>拉门技术: a标签包含一个span标签,a标签当左圆角，span为右圆角

>伪元素选择器：（类选择、伪类选择器就是选取对象）伪类选择器本质上就是插入一个元素(标签)只不过是行内标签

>transition： 要过度的属性  花费时间 运动曲线 何时开始

>转换中心点transform-origin:调整元素旋转中心