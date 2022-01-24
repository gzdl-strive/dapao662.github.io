---
title: diff处理不是同一节点时
date: 2022-01-09 12:07:24
toc: true
---

![3](/assets/vueImg/vueSource/snabbdom3.png "处理不是同一节点")

**如何定义“同一节点”**
```js
function sameVnode(vnode1, vnode2) {
  return vnode1.key === vnode2.key && vnode1.sel === vnode2.sel;
}
```
**旧节点的key要和新节点的key相同**且**旧节点的选择器要和新节点的选择器相同**

## createElement函数
>该函数用于创建节点
```js
const createElement = (vnode) => {
  //目的是把虚拟节点变成真实DOM
  //创建一个DOM节点，这个节点现在还是孤儿节点
  const domNode = document.createElement(vnode.sel);
  //判断该节点有子节点或文本
  if (vnode.text !== '' && vnode.text !== undefined && (vnode.children === undefined || vnode.children.length === 0)) {
    //内部是文本
    domNode.innserText = vnode.text;
  } else if (Array.isArray(vnode.children) && vnode.children.length > 0) {
    //内部是子节点，递归遍历节点
    //遍历children
    for (let i = 0; i < vnode.children; i++) {
      //得到当前这个children
      let ch = vnode.children[i];
      //创建当前子节点的DOM，一旦调用createElement意味着：创建出DOM了，并且它的elm属性指向了创建出的DOM，但是还没有上树，是一个孤儿节点。
      let chDOM = createElement(ch);
      //上树(domNode是父节点)
      domNode.appendChild(chDOM);
    }
  }

  //添加上虚拟节点的elm
  vnode.elm = domNode;

  //返回elm, elm属性是一个纯DOM对象
  return vnode.elm;
}

export default createElement;
```

## 第一版patch
```js
import vnode from './vnode';
import createElement from './createElement';

// patch.js
const patch = (oldVnode, newVnode) => {
  //判断oldVnode是DOM节点还是虚拟节点
  if (!oldVnode.hasOwnProperty('sel')) {
    //oldVnode是DOM节点，需要把该DOM节点包装成虚拟节点
    oldVnode = vnode(oldVnode.tagName.toLowerCase(), {}, [], undefined, oldVnode);
  }
  //判断oldVnode和newVnode是否是同一节点(通过判断key和sel属性)
  if (oldVnode.key === newVnode.key && oldVnode.sel === newVnode.sel) {
    //是同一节点，需要进行精细化比较
    console.log('是同一节点，需要进行更精细化比较');
    //暂不处理
  } else {
    //不是同一节点，暴力删除旧的，生成新的
    console.log('不是同一节点');
    const newVnodeElm = createElement(newVnode);

    //插入到老节点之前
    if (oldVnode.elm.parentNode && newVnodeElm) {
      oldVnode.elm.parentNode.insertBefore(newVnodeElm, oldVnode.elm);

      //删除老节点
      oldVnode.elm.parentNode.removeChild(oldVnode.elm);
    }
  }
}

export default patch;
```
