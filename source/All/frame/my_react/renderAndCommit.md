---
title: 渲染和提交阶段
date: 2021-08-15 10:46:02
toc: true
---

现在我们已经使用requestIdCallback函数和fiber数据结构构建了一棵fiber树和dom关联起来了

但是还存在一个问题：
 每次处理元素时，我们都会向DOM添加一个新的节点。
 但是浏览器可能会在我们完成渲染整个树之前中断我们的工作。
 那么这种情况下，**用户可能会看到不完整的UI**.
所以我们需要把performUnitOfWork中添加节点的代码移除。
然后我们使用`wipRoot(work in progress root)`跟踪fiber树的根节点.
然后一旦我们完成整个树的渲染(没有下一个工作单元了),我们就提交整个fiber树给DOM

**首先把performUnitOfWork函数中添加节点的代码移除**
```js
function performUnitOfWork(fiber) {
  if (!fiber.dom) {
    fiber.dom = createDom(fiber);
  }
  //移除这三行代码
  // if (fiber.parent) {
  //   fiber.parent.appendChild(fiber.dom);
  // }
}
```

**创建wipRoot，并初始化他**
```js
let nextUnitOfWork = null;//下一个工作单元
//新增
let wipRoot = null;//fiber树的根节点
```
```js
function render(element, container) {
  wipRoot = {
    dom: container,
    props: {
      children: [element],
    }
  }
  nextUnitOfWork = wipRoot;
}
```

**然后我们需要在workLoop循环结束后，判断是否可以提交**
```js
function workLoop(deadline) {
  //...
  while () {
    //...
  }
  //如果没有下一个工作单元,且存在fiber根节点，我们就把fiber提交给DOM
  if (!nextUnitOfWork && wipRoot) {
    commitRoot();
  }
}
```

**最后我们创建一个commitRoot函数用于提交**
因为我们要用递归的方式把fiber树种的节点都提交了，所以我们使用一个commitWork用于递归提交
```js
//提交根节点给dom
function commitRoot() {
  //根节点是root节点，不用提交，从它的child开始
  commitWork(wipRoot.child);
  wipRoot = null;
}
//递归提交到DOM上
function commitWork(fiber) {
  // 边界条件
  if (!fiber) {
    return;
  }
  const domParent = fiber.parent.dom;
  domParent.addendChild(fiber.dom);
  commitWork(fiber.child);
  commitWork(fiber.sibling);
}
```


