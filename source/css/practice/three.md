---
title: 响应式
date: 2020-11-08 20:12:51
tag: css
---

### 当屏幕小于500时div变成红色
![1](/assets/cssImg/1-6.png "1")
```css
<style>
    body {
        background-color: black;
    }
    .test{
        height: 200px;
        width: 200px;
        background-color: blue;
    }
    @media screen  and (max-width: 500px){
        .test{
            background: red; 
        }
    }
</style>
<div class="test"></div>
```

![2](/assets/cssImg/1-7.png "2")