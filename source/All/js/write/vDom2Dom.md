---
title: 虚拟DOM转真实DOM
date: 2022-08-10 09:31:24
toc: true
---

>vnode结构(虚拟节点结构)
```js
{
  tag,
  attrs,
  children
}
```

```js
function render(vnode, container) {
  container.appendChild(_render(vnode));
}

function _render(vnode) {
  // 如果是数字类型转化为字符串
  if (typeof vnode === 'number') {
    vnode = String(vnode);
  }
  // 字符串类型直接就是文本节点
  if (typeof vnode === 'string') {
    return document.createTextNode(vnode);
  }
  // 普通DOM
  const dom = document.createElement(vnode.tag);
  if (vnode.attrs) {
    // 遍历属性
    Object.keys(vnode.attrs).forEach(key => {
      const value = vnode.attrs[key];
      dom.setAttribute(key, value);
    });
  }
  // 子数组进行递归操作
  Array.isArray(vnode.children) && vnode.children.length > 0 && vnode.children.forEach(child => render(child, dom));
  return dom;
}
```

**测试**
```js
const vnode = {
  tag: 'div',
  attrs: {
    class: 'container',
  },
  children: [
    {
      tag: 'h1',
      attrs: {},
      children: ['hello']
    },
    {
      tag: 'span',
      attrs: {
        id: 'text',
        style: "color: red; font-size: 40px; background: aqua;"
      },
      children: ['span666']
    }
  ]
}

const body = document.querySelector('body');
render(vnode, body);
```
