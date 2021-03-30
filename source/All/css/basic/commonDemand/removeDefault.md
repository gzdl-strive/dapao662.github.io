---
title: 消除浏览器的默认样式
date: 2021-03-30 10:13:20
tog: css
toc: true
---

针对同一个类型的 HTML 标签，不同的浏览器往往有不同的表现，所以在网站制作的时候，开发者通常都是需要将这些浏览器的默认样式清除，让网页在不同的浏览器上能够保持一致。
针对清除浏览器默认样式这件事，在很早之前 CSS 大师 Eric A. Meyer 就干过。它就是写一堆通用的样式用来重置浏览器默认样式，这些样式通常会放到一个命名为 reset.css 文件中。比如大师的 reset.css 是这么写的：
```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
    margin: 0;
    padding: 0;
    border: 0;
    font-size: 100%;
    font: inherit;
    vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
    display: block;
}
body {
    line-height: 1;
}
ol, ul {
    list-style: none;
}
blockquote, q {
    quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
    content: '';
    content: none;
}
table {
    border-collapse: collapse;
    border-spacing: 0;
}
```
他的这份 reset.css 据说是被使用最广泛的重设样式的方案了。
除了 reset.css 外，后来又出现了 Normalize.css 。关于 Normalize.css, 其作者 necolas 专门写了一篇文章介绍了它，并谈到了它和 reset.css 的区别。这个是他写那篇文章的翻译版：让我们谈一谈 Normalize.css。
文章介绍到：Normalize.css 只是一个很小的CSS文件，但它在默认的 HTML 元素样式上提供了跨浏览器的高度一致性。相比于传统的 CSS reset，Normalize.css 是一种现代的、为 HTML5 准备的优质替代方案，现在已经有很多知名的框架和网站在使用它了。
Normalize.css 的具体样式可以看这里 [Normalize.css](https://necolas.github.io/normalize.css/latest/normalize.css)

区别于 reset.css，Normalize.css 有如下特点：
reset.css 几乎为所有标签都设置了默认样式，而 Normalize.css 则是有选择性的保护了部分有价值的默认值；
* 修复了很多浏览器的 bug，而这是 reset.css 没做到的；
* 不会让你的调试工具变的杂乱，相反 reset.css 由于设置了很多默认值，所以在浏览器调试工具中往往会看到一大堆的继承样式，显得很杂乱；
* Normalize.css 是模块化的，所以可以选择性的去掉永远不会用到的部分，比如表单的一般化；
* Normalize.css 有详细的说明文档；
