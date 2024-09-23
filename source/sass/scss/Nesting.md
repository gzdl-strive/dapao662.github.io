---
title: Nesting(嵌套)
date: 2020-11-06 20:55:24
tag: scss
---

>Sass允许您将选择器嵌套在选择器中

```scss
ul {
    list-style-type: none;
    margin-top: 2em;

    li {
        background-color: #FF0000;
    }
}
```
>'&' 将被父选择器替换.
You can also nest pseudo-classes. 您还可以嵌套伪类
Keep in mind that over-nesting will make your code less maintainable.请记住，过度嵌套会使您的代码难以维护
Best practices recommend going no more than 3 levels deep when nesting.最佳做法建议在嵌套时不要超过3个级别。

**例如：**
```scss
ul {
    list-style-type: none;
    margin-top: 2em;

    li {
        background-color: red;

        &:hover {
          background-color: blue;
        }

        a {
          color: white;
        }
    }
}
/* Compiles to: */

ul {
  list-style-type: none;
  margin-top: 2em;
}

ul li {
  background-color: red;
}

ul li:hover {
  background-color: blue;
}

ul li a {
  color: white;
}
```
