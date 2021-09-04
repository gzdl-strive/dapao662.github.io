---
title: fiber
date: 2021-08-15 10:13:02
toc: true
---

为了组织工作单元，我们需要一个数据结构:fiber
我们将为每个element使用一个Fiber,每个fiber都是一个工作单元
假设我们要渲染的元素树是这样的：
```js
  render(
    <div>
      <h1>
        <p />
        <a />
      </h1>
      <h2 />
    </div>,
    container
  )
```
在本次渲染中，我们将创建根fiber并将它设置为`nextUnitOfWork`
剩下的工作就都在`performUnitOfWork`函数中进行。在该函数中，我们应该做三件事：
- 1、add the element to the DOM(向DOM树添加元素)
- 2、create the fibers for the element's children(为当前fiber节点的孩子们创建fiber)
- 3、select the next unit of work(返回下一次循环要执行的fiber节点)

fiber这个`data structure`(数据结构)的目标之一就是**能很容易的找到下一个工作单元**。
也就是为什么**每个fiber**都有一个连接指向它的**第一个子节点、下一个兄弟节点、他的父节点**

>返回下一个工作单元的顺序是：它的孩子、兄弟、叔叔

**首先把render函数中的代码提取成createDom函数(专门用来创建DOM节点)**
我们需要把递归部分的代码去掉，createDom函数的作用只是用于创建dom,然后返回该dom
```js
function createDom(fiber) {
  const dom = 
    fiber.type === 'TEXT_ELEMENT'
      ? document.createTextNode('')
      : document.createElement(fiber.type);
  const isProperty = key => key !== 'children';
  Object.keys(fiber.props)
    .filter(isProperty)
    .forEach(name => {
      dom[name] = fiber.props[name];
    });
  return dom;
}
function render(element, container) {
  //TODO
}
```

**然后我们应该在render函数中初始化nextUnitOfWork值，使得workLoop中的循环能够开始**
```js
function render(element, container) {
  //设置根fiber
  nextUnitOfWork = {
    dom: container,//dom节点就是指向真实的dom,根fiber的dom就是root节点
    props: {
      children: [element],//element就是需要处理的子元素
    }
  }
}
let nextUnitOfWork = null;
```

现在我们的循环启动了，所以我们需要完善performUnitOfWork函数，使他能够生成dom节点，然后为子元素生成fiber节点，最后返回下一个待执行的工作单元。
```js
//该函数不仅执行当前工作单元(即创建当前fiber对应的dom节点)，然后返回下一个fiber
//fiber数据结构是我们构造出来的，目的是为了联系各工作单元，但是真实dom树上是没有对于的dom节点，所以我们需要构建dom节点
function performUnitOfWork(fiber) {
  //TODO add dom node(构建dom节点)
  //增加一个dom节点，先判断该fiber是否存在dom节点，没有dom节点的创建一个
  if (!fiber.dom) {
    //createDom函数的作用就是返回一个dom节点
    fiber.dom = createDom(fiber);
  }
  //现在我们只是把dom创建出来，没有添加到dom树上，所以我们需要把当前dom节点添加上去
  if (fiber.parent) {
    //每个fiber节点都会有child、sibling、parent分别执行第一个孩子、下一个兄弟、父亲节点
    fiber.parent.dom.appendChild(fiber.dom);
  }

  // 现在我们需要为当前fiber的孩子创建fiber
  //TODO create new fiber(创建新fiber)
  const elements = fiber.props.children;
  let index = 0;//用于循环结束和判断是否是第一个孩子
  let prevSibling = null;//用于保存上一个fiber节点，因为fiber.sibling会指向下一个兄弟节点
  while (index < elements.length) {
    const element = elements[index];
    const newFiber = {
      dom: null,//下一次循环会创建
      type: element.type,
      props: element.props,
      parent: fiber,//父亲是当前的fiber节点
    };
    //我们也需要把它添加到fiber树中，具体是child还是sibling取决于是否是第一个孩子
    //fiber节点分别由指向第一个孩子、下一个兄弟、父亲的指向
    if (index === 0) {
      //第一个孩子
      fiber.child = newFiber;
    } else {
      // 不是第一个孩子，就把前一个fiber节点的sibling指向当前循环的fiber子节点
      prevSibling.sibling = newFiber;
    }
    prevSibling = newFiber;//指向当前fiber
    index++;
  }

  //TODO return next of work(返回下一个工作单元)
  //前面我们介绍了，返回fiber节点的顺序为，1-孩子节点，2-兄弟节点，3-叔叔节点(为什么是叔叔而不是父亲，因为父亲节点已经执行过了)
  if (fiber.child) {
    return fiber.child;
  }
  //没有孩子，就找兄弟
  let nextFiber = fiber;
  while (nextFiber) {
    if (nextFiber.sibling) {
      return nextFiber.sibling;
    } else {
      //如果当前fiber没有兄弟,需要找到叔叔
      nextFiber = nextFiber.parent;
    }
  }
}
```
