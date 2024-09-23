---
title: diff处理新旧节点是同一节点时
date: 2022-01-09 13:31:24
toc: true
---

![4](/assets/vueImg/vueSource/snabbdom4.png "同一节点")

## patchVnode 函数(第一版)

> 对比同一虚拟节点(精细化比较)
> 不处理新旧虚拟节点都有 children 的情况

```js
import createElement from "./createElement";

//对比同一虚拟节点(精细化比较)
export default function patchVnode(oldVnode, newVnode) {
  //判断新旧虚拟节点是否是内存中同一对象
  if (oldVnode === newVnode) return;
  //判断新虚拟节点是否有text属性
  if (
    newVnode.text !== undefined &&
    (newVnode.children === undefined || newVnode.children.length === 0)
  ) {
    //新虚拟节点存在text属性
    console.log("新虚拟节点存在text属性");
    //判断旧节点的text和新节点的是否相同
    if (newVnode.text !== oldVnode.text) {
      //不同,将旧节点的text赋值为新的
      oldVnode.elm.innerText = newVnode.text;
    }
  } else {
    //新虚拟节点不存在text,即存在children
    console.log("新虚拟节点不存在text属性，即有children");
    //判断老的虚拟节点是否有children属性
    if (oldVnode.children !== undefined && oldVnode.children.length > 0) {
      //新的有children,老的也有children,这时最复杂的情况
      //暂不处理
    } else {
      //旧虚拟节点没有children，而新虚拟节点有children
      //1、清除旧的text
      oldVnode.elm.innerHTML = "";
      //遍历新虚拟节点，创建DOM，上树
      for (let i = 0; i < newVnode.children.length; i++) {
        let dom = createElement(newVnode.children[i]);
        oldVnode.elm.appendChild(dom);
      }
    }
  }
}
```

## diff算法的子节点更新策略
四种命中查找：
- ①新前与旧前
- ②新后与旧后
- ③新后与旧前(此种发生了，涉及移动节点，那么旧前所指向的节点要移动到旧后之后)
- ④新前与旧后(此种发生了，涉及移动节点，那么旧后所指向的节点要移动到旧前之前)
**命中一种就不再进行命中判断了，如果都没有命中，就需要用循环来寻找了。移动到`oldStartIdx`之前。**

## updateChildren 函数

> 处理新旧虚拟节点都有 children 的情况

```js
import patchVnode from "./patchVnode";
import createElement from "./createElement";

//判断是否是同一个虚拟节点
function checkSameVnode(a, b) {
  return a.sel === b.sel && a.key === b.key;
}

export default function updateChildren(parentElm, oldCh, newCh) {
  console.log("这是updateChildren");
  //旧前
  let oldStartIdx = 0;
  //新前
  let newStartIdx = 0;
  //旧后
  let oldEndIdx = oldCh.length - 1;
  //新后
  let newEndIdx = newCh.length - 1;
  //旧前节点
  let oldStartVnode = oldCh[0];
  //旧后节点
  let oldEndVnode = oldCh[oldEndIdx];
  //新前节点
  let newStartVnode = newCh[0];
  //新后节点
  let newEndVnode = newCh[newEndIdx];

  //keyMap映射
  let keyMap = null;

  //开始循环
  while (oldStartIdx <= oldEndIdx && newStartIdx <= newEndIdx) {
    //因为在后面遍历调整后，会把遍历的旧虚拟节点置为undefined,所以在进行新前旧前比较前，需要先判断是否为空
    if (oldStartVnode === null || oldCh[oldStartIdx] === undefined) {
      oldStartVnode = oldCh[++oldStartIdx];
    } else if (oldEndVnode === null || oldCh[oldEndIdx] === undefined) {
      oldEndVnode = oldCh[--oldEndIdx];
    } else if (checkSameVnode(oldStartVnode, newStartVnode)) {
      //新前旧前
      console.log("①新前旧前命中");
      patchVnode(oldStartVnode, newStartVnode);
      oldStartVnode = oldCh[++oldStartIdx];
      newStartVnode = newCh[++newStartIdx];
    } else if (checkSameVnode(oldEndVnode, newEndVnode)) {
      //新后旧后
      console.log("②新后旧后命中");
      patchVnode(oldEndVnode, newEndVnode);
      oldEndVnode = oldCh[--oldEndIdx];
      newEndVnode = newCh[--newEndIdx];
    } else if (checkSameVnode(oldStartVnode, newEndVnode)) {
      //新后旧前
      console.log("③新后旧前命中");
      patchVnode(oldStartVnode, newEndVnode);
      //新后与旧前命中，此时需要移动节点，移动旧前指向的节点到旧后节点的后面
      // 如何移动节点？？只要你插入一个已经在DOM树上的节点，它就会被移动
      parentElm.insertBefore(oldStartVnode.elm, oldEndVnode.elm.nextSibling);
      oldStartVnode = oldCh[++oldStartIdx];
      newEndVnode = newCh[--newEndIdx];
    } else if (checkSameVnode(oldEndVnode, newStartVnode)) {
      //新前旧后
      console.log("④新前旧后命中");
      patchVnode(oldEndVnode, newStartVnode);
      //新前与旧后命中，此时需要移动节点，移动旧后指向的节点到旧前之前
      parentElm.insertBefore(oldEndVnode.elm, oldStartVnode.elm);
      oldEndVnode = oldCh[--oldEndIdx];
      newStartVnode = newCh[++newStartIdx];
    } else {
      //四种命中都没有命中
      //制作keyMap一个映射对象，这样就不用每次都遍历老对象了
      if (!keyMap) {
        keyMap = {};
        for (let i = oldStartIdx; i <= oldEndIdx; i++) {
          const key = oldCh[i].key;
          key && (keyMap[key] = i);
        }
      }
      console.log("keyMap: ", keyMap);
      //寻找当前这项(newStartIdx)在keyMap中的映射的位置序号
      const idxInOld = keyMap[newStartVnode.key];
      if (idxInOld === undefined) {
        //在旧虚拟节点中没有找到，说明这是一个新节点
        //被加入的项(就是newStartVnode这项)现在还不是真正的节点
        //插入到旧前的前面
        parentElm.insertBefore(createElement(newStartVnode), oldStartVnode.elm);
      } else {
        //不是新节点，那么需要移动
        const elmToMove = oldCh[idxInOld];
        patchVnode(elmToMove, newStartVnode);
        //把这项设置为undefined,表示我已经处理完这项了
        oldCh[idxInOld] = undefined;
        //移动，调用insertBefore,移动到旧前的前面
        parentElm.insertBefore(elmToMove.elm, oldStartVnode.elm);
      }
      //还需要把新前指针下移
      newStartVnode = newCh[++newStartIdx];
    }
  }

  //循环结束(新增或删除)
  if (newStartIdx <= newEndIdx) {
    console.log("newVnode还有剩余节点没有处理，要加项");
    //遍历
    for (let i = newStartIdx; i <= newEndIdx; i++) {
      parentElm.insertBefore(createElement(newCh[i]), oldCh[oldStartIdx]);
    }
  } else if (oldStartIdx <= oldEndIdx) {
    console.log("old还有剩余节点没有处理，要删除项");
    //批量删除oldStart和oldEnd指针之间的项
    for (let i = oldStartIdx; i <= oldEndIdx; i++) {
      parentElm.removeChild(oldCh[i].elm);
    }
  }
}
```

## patchVnode 函数(第二版)

```js
import updateChildren from "./updateChildren";

//...
if (oldVnode.children !== undefined && oldVnode.children.length > 0) {
  //老的有children，新的也有children,这是最复杂的情况
  //因为这里是同一节点，第一个参数既可以是oldVnode的elm，也可以是newVnode的elm
  updateChildren(oldVnode.elm, oldVnode.children, newVnode.children);
}
//...
```

## patch 函数(第二版)

```js
import patchVnode from "./patchVnode";

//...
//判断oldVnode和newVnode是不是同一节点(通过判断key和sel是否相同)
if (oldVnode.key === newVnode.key && oldVnode.sel === newVnode.sel) {
  console.log("是同一节点，需要进行更精细化比较");
  //精细化比较
  patchVnode(oldVnode, newVnode);
}

//...
```
