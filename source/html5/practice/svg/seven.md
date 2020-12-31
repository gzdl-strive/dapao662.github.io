---
title: raphael
date: 2020-12-31 09:46:19
tag: SVG
---

```html
<script src="https://cdn.bootcdn.net/ajax/libs/raphael/2.3.0/raphael.min.js"></script>
<script>
    window.onload = function() {
        let paper = Raphael(0, 0, 800, 600);//位置 宽 高
        // paper.rect(100, 100, 300, 200).attr('transform', 'r20s2,1');
        let rect = paper.rect(100, 100, 300, 200).attr('fill', 'yellow').click(function() {
            rect.animate({'transform': 's2,1r30'}, 3000, 'bounce');
        })
    }
</script>
```

