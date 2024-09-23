---
title: 并发模式
date: 2021-08-15 09:51:02
toc: true
---

上一节我们已经使用了递归调用的方式完成了DOM节点的创建挂载到容器上了，但是递归调用方式存在问题，那就是当我们的渲染开始了，就不能停止了，直到我们渲染了完整的`element tree`.如果`element tree`很大，就会阻塞主线程时间太久。而此时如果浏览器需要做优先级比较高的任务，比如用户输入或动画，就必须等到渲染完成才能执行。

所以我们要将工作分解为一个个小单元，在我们完成每个单元后，如果还有其他事情需要做，我们会让浏览器中断渲染。

我们使用`requestIdleCallback`来做一个循环。浏览器会在主线程空闲时运行回调。
react已经不使用requestIdleCallback，它使用`scheduler`包，但是对于我们这个例子来说，其实是差不多的。

`window.requestIdleCallback()`方法将在浏览器的空闲时段内调用的函数排队。
这使开发者能够在主事件循环上执行后台和低优先级工作，而不会影响延迟关键事件，如动画和输入响应。
语法：
```js
  var handle = window.requestIdleCallback(callback[, options])
```
返回值：一个ID,可以把它传入`window.cancelIdleCallback()`方法来结束回调
>参数：
- callback: 一个在事件循环空闲时即将被调用的函数的引用。
  - 函数会接收到一个名为 IdleDeadline 的参数，这个参数可以获取当前空闲时间以及回调是否在超时时间前已经执行的状态。
- options：可选，包括可选的配置参数。

```js
function render(elelent, container) {
  const dom = element.type === 'TEXT_ELEMENT' ? document.createTextNode('') : document.createElement(element.type);

  const isProperty = key => key !== 'children';
  Object.keys(element.props)
    .filter(isProperty)
    .forEach(name => {
      dom[name] = element.props[name];
    });
  
  element.props.children.forEach(child => (
    render(child, dom);
  ));

  container.appendChild(dom);
}

let nextUnitOfWork = null;//下一个工作单元

//requestIdleCallback回调函数，用于在浏览器空闲时间处理
//因为我们使用递归回调时不能停止渲染，所以我们在该回调中渲染就会在浏览器的空闲时间进行
function workLoop(deadline) {
  let shouldYield = false;//标志位-判断浏览器是否还有剩余时间
  //当有下一个工作单元要处理且浏览器有空余时间
  while (nextUnitOfWork && !shouldYield) {
    nextUnitOfWork = performUnitOfWork(
      nextUnitOfWork
    )
    shouldYield = deadline.timeRemaining() < 1;
  }
  requestIdleCallback(workLoop);
}

requestIdleCallback(workLoop);

//该函数需要执行当前工作单元并返回下一个工作单元
function performUnitOfWork(nextUnitOfWork) {
  //TODO ...
}
```
