---
title: Partials and Imports(部分和导入)
date: 2020-11-06 21:04:24
tag: scss
---

>Sass允许您创建部分文件。这可以帮助您保持Sass代码的模块化。部分文件应以“_”开头，例如`_reset.css`。部分内容不会生成到CSS中。
```scss
/*_reset.scss*/
html, body, ul, ol {
    margin: 0;
    padding: 0;
}
$primary-color: #00f;
.abc {
    color: $primary-color;
}
```

>Sass提供了`@import`，可用于将部分导入到文件中。这不同于传统的`CSS @import`语句，后者发出另一个HTTP请求以提取导入的文件。Sass提取导入的文件，并将其与编译后的代码合并。
```scss
/*demo.scss*/
@import 'reset';

body {
    font-size: 16px;
    font-family: 'Courier New', Courier, monospace;;
}

/*Compiles to: */
html, body, ul, ol {
  margin: 0;
  padding: 0;
}

.abc {
  color: #00f;
}

body {
  font-size: 16px;
  font-family: "Courier New", Courier, monospace;
}
```