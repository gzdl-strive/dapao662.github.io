---
title: Control Directives(控制指令)
date: 2020-11-06 16:30:24
tag: scss
---

> `Sass`允许你使用@if,@else,@for,@while和@each来控制将代码编译成`CSS`

### @if/@else 
```scss
$debug: true !default;
@mixin debugmode {
    @if $debug {
        @debug "Debug mode enabled";
        display: inline-block;
    }
    @else {
        display: none;
    }
}
.info {
    @include debugmode;
}
/*
如果$debug设置为true,则显示.info。否则不会显示.info。
注意：@debug将调试信息输出到命令行。在调试SCSS时检查变量很有用.
*/
```

### @for
>`@for`是一个循环访问一系列值的控制循环.在项目集合上设置样式时特别有用。有两种形式，`through`和`to`。前者包括最后一个值，后者不包括最后一个值。

```scss
@for $c from 1 to 4 {
    div:nth-of-type(#{$c}) {
        left: ($c - 1) * 900 / 3;
    }
}

@for $c from 1 through 3 {
    .myclass-#{$c} {
        color: rgb($c * 255 / 3, $c * 255 / 3, $c * 255 / 3);
    }
}

/*will compile to:*/
div:nth-of-type(1) {
    left: 0;
}

div:nth-of-type(2) {
    left: 300;
}

div:nth-of-type(3) {
    left: 600;
}

.myclass-1 {
    color: #555555;
}

.myclass-2 {
    color: #aaaaaa;
}

.myclass-3 {
    color: white;
// SASS自动把#FFFFFF转换为white
}
```

### @while
```scss
$columns: 4;
$column-width: 80px;
@while $columns > 0 {
    .col-#{$columns} {
        width: $column-width;
        left: $column-width * ($columns - 1);
    }
    $columns: $columns -1
}

/*will output the following CSS:*/
.col-4 {
    width: 80px;
    left: 240px;
}

.col-3 {
    width: 80px;
    left: 160px;
}

.col-2 {
    width: 80px;
    left: 80px;
}

.col-1 {
    width: 80px;
    left: 0px;
}
```

### @each
>@`each`函数类似于`@for`，除了使用列表而不是序数值外。Note:您可以像其他变量一样指定列表，并使用空格作为分隔符。
```scss
$social-links: facebook twitter linkedin reddit;
.social-links {
    @each $m in $social-links {
        .icon-#{$sm} {
            background-image: url("image/#{$sm}.png");
        }
    }
}
/*which will output: */
.social-links .icon-facebook {
    background-image: url("images/facebook.png");
}

.social-links .icon-twitter {
    background-image: url("images/twitter.png");
}

.social-links .icon-linkedin {
    background-image: url("images/linkedin.png");
}

.social-links .icon-reddit {
    background-image: url("images/reddit.png");
}
```



