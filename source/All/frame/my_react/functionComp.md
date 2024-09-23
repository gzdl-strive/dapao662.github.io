---
title: 函数组件
date: 2021-08-18 09:10:02
toc: true
---

下一件要做的事就是支持函数组件
函数组件在两个方面有所不同：
1. 函数组件的fiber没有DOM节点
2. 并且孩子们是从直接运行该函数得出的，而不是从props中

我们需要检查fiber类型是否是一个函数，并调用不同的更新函数
在`updateFunctionComponent`我们运行函数来获取孩子(是个函数)
`<App />`返回的类型是`function App`,所以我们可以运行`fiber.type`来返回结果，一旦返回结果了，就和之前一样处理

我们这边还需要改变的是`commitWork`函数
**现在我们有了没有DOM节点的fiber**,我们需要改变两件事。
>1、首先为了找到一个DOM节点的父节点，我们需要沿着fiber树向上移动，直到找到一个带有DOM节点的fiber
>2、当删除一个节点时，我们还需要沿着fiber树向下移动，直到找到一个具有DOM节点的子节点

**先判断当前节点类型是否是函数**
之前我们在`performUnitOfWork`中是直接判断当前节点是否存在dom节点,没有dom的话，我们会创建它，现在新增函数组件，所以我们需要先判断当前节点是否是一个函数
```js
function performUnitOfWork(fiber) {
  //新增：判断当前节点是否是一个函数
  const isFunctionComponent = fiber.type instanceof Function;
  if (isFunctionComponent) {
    //是函数组件,调用updateFuncitonComponent函数
    updateFunctionComponent(fiber);
  } else {
    //和之前一样，不过我们可以把之前的代码提取成一个函数,叫做updateHostComponent
    updateHostComponent(fiber);
  }

  //...
}
function updateHostComponent(fiber) {
  if (!fiber.dom) {
    fiber.dom = createDom(fiber);
  }
  const elements = fiber.props.children;
  reconcileChildren(fiber, elements);
}
function updateFunctionComponent(fiber) {
  //现在fiber是一个函数，fiber的type就是该函数名
  //所以我们直接运行fiber.type，就可以得到它的children
  const children = [fiber.type(fiber.props)];
  //接下来就和之前一样了
  reconcileChildren(fiber, children);
}
```

**现在我们还需要处理一下`commitWork`函数**
```js
function commitWork(fiber) {
  if (!fiber) {
    return;
  }
  //现在可能存在没有DOM节点的fiber,所以我们需要沿着fiber树向上移动，直到找到一个带有DOM节点的fiber
  //新增
  let domParentFiber = fiber.parent;
  while (!domParentFiber.dom) {
    domParentFiber = domParentFiber.parent;
  }

  const domParent = domParentFiber.dom;
  if (fiber.effectTag === 'PLACEMENT' && fiber.dom !== null) {
    domParent.appendChild(fiber.dom);
  } else if (fiber.effectTag === 'UPDATE' && fiber.dom !== null){
    updateDom(
      fiber.dom,
      fiber.alternate.props,
      fiber.props
    )
  } else if (fiber.effectTag === 'DELETION') {
    //当删除一个节点时，我们需要向下移动，直到找到一个具有DOM节点的子节点
    //之前我们的做法是直接移除这个节点，现在我们创建一个commitDeletion函数用于找到并删除
    commitDeletion(fiber, domParent);
  } 
}
function commitDeletion(fiber, domParent) {
  if (fiber.dom) {
    domParent.removeChild(fiber.dom);
  } else {
    //递归向下查找
    commitDeletion(fiber.child, domParent);
  }
}
```

