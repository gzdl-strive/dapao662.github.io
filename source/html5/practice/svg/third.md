---
title: svg事件
date: 2020-12-30 16:32:19
tag: SVG
---

>事件-修改
    添加事件    --一样
    修改样式    --一样
    属性操作    --.setAttribute,.getAttribute
    矢量图都没有单位

```html
<body>
    <svg width="800" height="500">
        <line x1="100" y1="100" x2="300" y2="300" style="stroke: red;stroke-width: 20" id="l1"></line>
    </svg>
</body>
<script>
    window.onload = function() {
        let oL1 = document.getElementById('l1');
        oL1.onClick = function() {
            alert(oL1.getAttribute('x1'));
            oL1.setAttribute('x1', '200');
        }
    }
</script>
```



