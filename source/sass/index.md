---
title: sass
date: 2020-11-06 15:29:36
tag: sass
---

## 简介：
>Sass是一种CSS扩展语言，它添加了诸如变量，嵌套，mixin等功能。Sass（和其他预处理器，例如Less）可帮助开发人员编写可维护的DRY（Don't Repeat Yourself）代码

>Sass有两种不同的语法选项可供选择。SCSS，其语法与CSS相同，但具有Sass的附加功能。或Sass（原始语法），它使用缩进而不是花括号和分号。本教程是使用SCSS编写的。

>如果您已经熟悉CSS3，则可以相对较快地使用Sass。它没有提供任何新的样式属性，而是提供了更有效地编写CSS并使维护更加容易的工具。

>首先使用npm包管理工具下载sass，执行`npm install sass -D`在本地目的下载sass,编写后缀名为`.scss`的文件,使用命令`sass input.scss output.css`将会把` Sass `文件` input.scss `编译输出为` output.css`

>你还可以利用 `--watch `参数来监视单个文件或目录。` --watch `参数告诉` Sass `监听源文件的变化， 并在每次保存 `Sass `文件时重新编译为` CSS`。如果你只是想监视 （而不是手动构建）`input.scss `文件，你只需在` sass `命令后面添加 `--watch` 参数即可，如下：
`sass --watch input.scss output.css`
可以使用文件夹路径作为输入和输出， 并使用冒号分隔它们，来监听文件并输出到目录。例如:
`sass --watch app/sass:public/stylesheets`Sass 将会监听 app/sass 目录下所有文件的变动，并 编译 CSS 到 public/stylesheets 目录下

## 变量 
>[变量(Variables)](/sass/scss/Variables "变量")

## 控制指令
>[控制指令(Control Directives)](/sass/scss/ControlDirectives "控制指令")

## 混合
>[混合(Mixins)](/sass/scss/Mixins "混合")

## 函数
>[函数(Functions)](/sass/scss/Functions "函数")

## 继承
>[继承(Extend)](/sass/scss/Extend "继承")

## 嵌套
>[嵌套(Nesting)](/sass/scss/Nesting "嵌套")

## 部分和导入
>[部分和导入(Partials and Imports)](/sass/scss/Partials-Imports "Partials and Imports")

## 占位符选择器
>[占位符选择器(Placeholder Selectors)](/sass/scss/Placeholder-Selectors "占位符选择器")

## 数学运算
>[数学运算(Math Operations)](/sass/scss/Math-Operations "数学运算")

---------------------------------------------
### 聚光灯效果
>[聚光灯](/sass/practice/first "聚光灯")

### 水滴图
>[水滴图](/sass/practice/second "水滴图")

