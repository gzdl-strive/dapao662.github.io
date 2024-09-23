---
title: perserve-3d
date: 2020-12-31 10:54:19
tag: CSS
---

>perserve-3d：给父级去掉限制，使用transform-style: perserve-3d

```html
<style>
    .d1 {
        width: 200px;
        height: 200px;
        background: #ccc;
        margin: 10px auto;
        transform: perspective(700px) rotateX(60deg) rotateZ(40deg);
        /* 给父级去掉限制 */
        transform-style: preserve-3d;
    }
    .child{
        width: 100%;
        height: 100%;
        background: greenyellow;
        transition:1s all ease-in;
        transform:translateZ(0px);
        /*给父级去掉限制*/
        transform-style: preserve-3d;
    }
    .child:hover{
        transform:translateZ(100px);
    }
    .child2{
        width: 50%;
        height: 50%;
        background: deepskyblue;
        transition:1s all ease-in;
        transform:translateZ(0px);
    }
    .child2:hover{
        transform:translateZ(50px);
    }
</style>
<body>
    <div class="d1">
        <div class="child">
            <div class="child2"></div>
        </div>
    </div>
</body>
```

![5](/assets/css3Img/5.gif "CSS3-preserve-3d")