---
title: Hooks-useState实现
date: 2021-08-19 09:29:02
toc: true
---

现在我们已经可以处理函数组件了，现在让我们新增状态state
因为我们是在函数组件中使用的useState,当函数执行时才会调用
```js
//之前我们处理函数组件是在该函数中处理的
function updateFunctionComponent(fiber) {
  const children = [fiber.type(fiber.props)];
  reconcileChildren(fiber, children);
}
//现在我们先新增一个useState方法-useState接收一个初始化参数可以是数字、字符串、数组、对象...
function useState(initial) {
  //TODO
}
```

我们还需要在调用函数组件之前初始化一些变量，以便于我们在useState时使用
```js
//新增
let wipFiber = null;
let hookIndex = null;

function updateFunctionComponent(fiber) {
  //初始化变量
  wipFiber = fiber;
  hookIndex = 0;
  wipFiber.hooks = [];
  ...
}
```

当函数组件调用useState时，我们检查是否有旧的钩子。我们使用hook的index来检查fiber的alternate
如果有旧的hook,我们拷贝旧fiber的状态到新fiber,如果没有旧hook,我们就初始化状态
然后我们给fiber增加new Hook,将Hook索引加1，然后返回状态
```js
function useState(initial) {
  const oldHook = 
    wipFiber.alternate &&
    wipFiber.alternate.hooks && 
    wipFiber.alternate.hooks[hookIndex];
  const hook = {
    state: oldHook ? oldHook.state : initial
  }

  wipFiber.hooks.push(hook);
  hookIndex++;
  return [hook.state];
}
```

useState也应该返回一个更新状态的函数，所以我们定义setState函数来接收action
我们把这个action推到我们增加hook的队列中
然后我们做一些类似于我们在render函数中所做的事情，将一个新的正在进行的工作根设置为下一个工作单元，这样工作循环就可以开始一个新的渲染阶段。
```js
function useState(initial) {
  const oldHook = 
    wipFiber.alternate &&
    wipFiber.alternate.hooks && 
    wipFiber.alternate.hooks[hookIndex];
  const hook = {
    state: oldHook ? oldHook.state : initial,
    queue: [],
  }

  const setState = action => {
    hook.queue.push(action);
    wipRoot = {
      dom: currentRoot.dom;
      props: currentRoot.props,
      alternate: currentRoot,
    }
    nextUnitOfWork = wipRoot;
    deletions = [];
  }

  wipFiber.hooks.push(hook);
  hookIndex++;
  return [hook.state, setState];
}
```

现在我们还没有运行该action
我们在下一次渲染组件时这样做，我们从旧的钩子队列中获取所有的动作，然后将它们一一应用到新的钩子状态，所以当我们返回状态时，它会被更新。
```js
function useState(initial) {
  const oldHook = 
    wipFiber.alternate &&
    wipFiber.alternate.hooks && 
    wipFiber.alternate.hooks[hookIndex];
  const hook = {
    state: oldHook ? oldHook.state : initial,
    queue: [],
  }

  //新增，运行action
  const actions = oldHook ? oldHook.queue : [];
  actions.forEach(action => {
    hook.state = action(hook.state);
  })

  const setState = action => {
    hook.queue.push(action);
    wipRoot = {
      dom: currentRoot.dom;
      props: currentRoot.props,
      alternate: currentRoot,
    }
    nextUnitOfWork = wipRoot;
    deletions = [];
  }

  wipFiber.hooks.push(hook);
  hookIndex++;
  return [hook.state, setState];
}
```

至此，我们的react已经写完了
