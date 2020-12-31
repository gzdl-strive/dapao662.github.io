---
title: js创建svg
date: 2020-12-31 9:09:19
tag: SVG
---

>JS生成元素
document.createElement  创建HTML元素
NS-->Name Space(document.createElementNS('http://www.w3.org/2000/svg','line'))
document.createElement=>document.createElementNS("http://www.w3.org/1999/xhtml")
如果要创建svg标签也要用NS

```html
<body>
    <input type="button" value="点击" id="btn1"/><br />
    <svg width="800" height="600" id="svg1"><line x1="200" y1="150" x2="250" y2="300" style="stroke: yellow;"></line></svg>
</body>
<script>
    window.onload = function() {
        let oB = document.getElementById('btn1');
        let oSvg = document.getELementById('svg1');

        oB.onclick = function() {
            let oL = document.createElementNS('http://www.w3.org/2000/svg', 'line');
            oL.setAttribute('x1', '100');
            oL.setAttribute('y1', '100');
            oL.setAttribute('x2', '300');
            oL.setAttribute('y2', '500');

            oL.style.stroke = 'red';
            oSvg.appendChild(oL);
        }
    }
</script>
```