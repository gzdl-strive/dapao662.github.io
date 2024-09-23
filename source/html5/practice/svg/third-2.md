---
title: svg事件
date: 2020-12-30 16:41:19
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
        <line x1="100" y1="100" x2="300" y2="300" style="stroke: red;stroke-width: 20" id="l1"></line></svg>
</body>
<script>
    window.onload = function() {
        let oL1 = document.getElementById('l1');
        // 方法一
        oL1.onmouseover = function () {
            this.style.stroke = 'yellow';
        }
        oL1.onmouseout = function() {
            this.style.stroke = 'red';
        }
        // 方法二
        oL1.addEventListener('mouseover', function() {
            this.style.stroke = 'yellow';
        });
        oL1.addEventListener('mouseout', function() {
            this.style.stroke = 'red';
        });
        // 方法三
        document.body.onmouseover = function(ev) {
            if (ev.srcElement === oL1) {
                ev.srcElement.style.stroke = 'yellow';
            }
        }
        document.body.onmouseout = function(ev) {
            if (ev.srcElement === oL1) {
                ev.srcElement.style.stroke = 'red';
            }
        }
    }
</script>
```



