---
title: 矩形
date: 2020-12-30 16:50:19
tag: SVG
---

><rect x y width height rx ry>

```html
<body>
    <svg width="800" height="500">
        <rect x="100" y="100" width="300" height="300" rx="5" ry="5" 
            style="stroke: red; stroke-width: 20; fill: rgba(0, 0, 0, 0)" onclick="alert('a')">
        </rect>
    </svg>
    <!-- fill为none时点击fill区域不会触发alert -->
</body>
```