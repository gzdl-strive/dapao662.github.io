---
title: 简单介绍下虚拟DOM和diff算法
date: 2022-01-09 10:50:24
toc: true
---

![1](/assets/vueImg/vueSource/snabbdom1.png)

## 虚拟 DOM

**真实 DOM**

```html
<div class="box">
  <h3>我是一个标题</h3>
  <ul>
    <li>Test1</li>
    <li>Test2</li>
    <li>Test3</li>
  </ul>
</div>
```

**虚拟 DOM**

```json
{
  "sel": "div",
  "data": {
    "class": { "box": true }
  },
  "children": [
    {
      "sel": "h3",
      "data": {},
      "text": "我是一个标题"
    },
    {
      "sel": "ul",
      "data": {},
      "children": [
        { "sel": "li", "data": {}, "text": "test1" },
        { "sel": "li", "data": {}, "text": "test2" },
        { "sel": "li", "data": {}, "text": "test3" },
      ]
    }
  ]
}
```

## diff是发生在虚拟DOM上的
>新虚拟DOM和老虚拟DOM进行diff(精细化比较),算出应该如何最小量更新，最后反映到真正的DOM上。
