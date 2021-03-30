---
title: 视觉格式化模型
date: 2021-03-29 17:48:20
tog: css
toc: true
---

## 视觉格式化模型
视觉格式化模型（Visual formatting model）是用来处理和在视觉媒体上显示文档时使用的计算规则。CSS 中一切皆盒子，而视觉格式化模型简单来理解就是规定这些盒子应该怎么样放置到页面中去，这个模型在计算的时候会依赖到很多的因素，比如：盒子尺寸、盒子类型、定位方案（是浮动还是定位）、兄弟元素或者子元素以及一些别的因素。
![7](/assets/cssImg/allBasic/7.png "视觉格式化模型")
从上图中可以看到视觉格式化模型涉及到的内容很多，有兴趣深入研究的可以结合上图看这个 W3C 的文档 Visual formatting model。所以这里就简单介绍下盒子类型。
![8](/assets/cssImg/allBasic/8.png)
盒子类型由 display 决定，同时给一个元素设置 display 后，将会决定这个盒子的 2 个显示类型（display type）：
* outer display type（对外显示）：决定了该元素本身是如何布局的，即参与何种格式化上下文；
* inner display type（对内显示）：其实就相当于把该元素当成了容器，规定了其内部子元素是如何布局的，参与何种格式化上下文；

### outer display type
对外显示方面，盒子类型可以分成 2 类：`block-level box（块级盒子）` 和 `inline-level box（行内级盒子）`。
依据上图可以列出都有哪些块级和行内级盒子：
* 块级盒子：display 为 block、list-item、table、flex、grid、flow-root 等；
* 行内级盒子：display 为 inline、inline-block、inline-table 等；

**所有块级盒子都会参与 BFC，呈现垂直排列；而所有行内级盒子都参会 IFC，呈现水平排列。**
除此之外，block、inline 和 inline-block 还有什么更具体的区别呢？
**block**
* 占满一行，默认继承父元素的宽度；多个块元素将从上到下进行排列；
* 设置 width/height 将会生效；
* 设置 padding 和 margin 将会生效；

**inline**
* 不会占满一行，宽度随着内容而变化；多个 inline 元素将按照从左到右的顺序在一行里排列显示，如果一行显示不下，则自动换行；
* 设置 width/height 将不会生效；
* 设置竖直方向上的 padding 和 margin 将不会生效；

**inline-block**
* 是行内块元素，不单独占满一行，可以看成是能够在一行里进行左右排列的块元素；
* 设置 width/height 将会生效；
* 设置 padding 和 margin 将会生效；

### inner display type
对内方面，其实就是把元素当成了容器，里面包裹着文本或者其他子元素。container box 的类型依据 display 的值不同，分为 4 种：
* block container：建立 BFC 或者 IFC；
* flex container：建立 FFC；
* grid container：建立 GFC;
* ruby container：接触不多，不做介绍。
值得一提的是如果把 img 这种替换元素（replaced element）申明为 block 是不会产生 container box 的，因为替换元素比如 img 设计的初衷就仅仅是通过 src 把内容替换成图片，完全没考虑过会把它当成容器。
