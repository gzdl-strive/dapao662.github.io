---
title: 协调
date: 2021-08-16 11:26:02
toc: true
---

现在我们只能向DOM树添加东西，那么更新和删除节点呢？
`we need to compare the elements we receive on the render function to the last fiber tree we committed to the DOM.`
我们需要将在渲染函数上接收的元素与提交给DOM的最后一棵fiber树进行比较,所以我们需要在提交后保存上一个提交给DOM的fiber树的引用，我们叫他`currentRoot`
我们也给每个fiber添加`alternate`属性alternate属性链接old fiber,该fiber是我们在之前提交阶段提交给DOM的节点

我们把`peformUnitOfWork`中创建fiber的代码提取处理到`reconcileChildren`中，该函数用于比较，然后进行添加节点、更新和删除

`reconcileChildren`: 协调旧fiber和新元素
为了比较它们：
- 1、如果旧fiber和新元素有相同类型(更新它的属性)`effectTag: UPDATE`
- 2、类型不同，且存在一个新元素(创建一个新fiber)`effectTag: PLACEMENT`
- 3、类型不同，存在旧元素(删除旧元素)(我们使用一个deletions数组用于存放待删除的fiber旧节点)`effectTag: DELETION`
  (effectTag属性用于在提交阶段判断当前节点是什么状态(更新、新增、删除))
fiber节点处理完后，进入到提交(commit phase)阶段
提交之前先移除deletions里的节点

>这边需要注意的是：更新节点，需要移除旧属性，然后设置新属性，还需要注意的是事件监听器属性，以on开头


**首先新增currentRoot和alternate属性，创建一个deletions用于保存待删除fiber节点(并在render函数中初始化他)**
```js
function commitRoot() {
  commitWork(wipRoot.child);
  //新增,currentRoot:保存上一个提交给DOM的fiber树的引用
  currentRoot = wipRoot;
  wipRoot = null;
}

function render(element, container) {
  wipRoot = {
    dom: container,
    props: {
      children: [element],
    },
    //新增alternate: 链接old fiber
    alternate: currentRoot,
  }
  deletions = [];
  nextUnitOfWork = wipRoot;
}

let nextUnitOfWork = null;
let wipRoot = null;
//新增
let currentRoot = null;//保存上一个提交给DOM的fiber树的引用
//新增
let deletions = null;//用于保存待删除fiber节点的数组
```

**然后我们需要把performUnitOfWork中创建fiber的节点提取出来，在reconcileChildren中统一处理**
```js
function performUnitOfWork(fiber) {
  //TODO add dom node
  ...

  //TODO create new fiber
  const elements = fiber.props.children;
  reconcileChildren(fiber, elements);

  //TODO return next unit of work
  ...
}
function reconcileChildren(wipFiber, elements) {
  let index = 0;
  let oldFiber = wipFiber.alternate && wipFiber.alternate.child;
  let prevSibling = null;
  while (index  < elements.length || oldFiber !== null) {
    const element = elements[index];
    let newFiber = null;
    //比较旧fiber和新元素
    //1、类型相同(更新props)
    //2、类型不同且由新元素(创建一个新元素)
    //3、类型不同且旧fiber（删除旧节点）
    const sameType = oldFiber && element && element.type === oldFiber.type;

    if (sameType) {
      //TODO Update the node
      newFiber = {
        type: oldFiber.type,
        props: element.props,
        dom: oldFiber.dom,
        parent: wipFiber,
        alternate: oldFiber,
        effectTag: 'UPDATE',
      }
    }
    if (!sameType && element) {
      //TODO add this node
      newFiber = {
        type: element.type,
        props: element.props,
        dom: null,
        parent: wipFiber,
        alternate: null,
        effectTag: 'PLACEMENT',
      }
    }
    if(!sameType && oldFiber) {
      //TODO delete the oldFiber's node
      //这边没有新元素，所以为旧fiber添加effectTag
      oldFiber.effectTag = 'DELETION';
      //不直接删除，在提交时一起删除，使用一个数组存储
      deletions.push(oldFiber);
    }

    //判断是否是第一个孩子
    if (index === 0) {
      wipFiber.child = newFiber;
    } else {
      prevSibling.sibling = newFiber;
    }
    prevSibling = newFiber;
    index++;
  }
}
```

此时fiber阶段已经处理完了，我们已经成功判断它到底是应该更新、删除、新增，并使用一个effectTag属性存储它的类型
fiber阶段处理完了，进入到提交阶段了
**然后就应该在提交阶段根据effectTag属性进行处理**

在提交之前，应该先移除deletions里面的节点
```js
function commitRoot() {
  //新增
  deletions.forEach(commitWork);
  //TODO add nodes to dom
  ...
}
//之前commitWork是用于递归提交fiber节点到DOM上
//所以我们应该修改commitWork函数，根据effectTag的不同进行不同处理
function commitWork(fiber) {
  //边界条件
  if (!fiber) return;
  const domParent = fiber.parent.dom;

  //新增
  //如果fiber的effectTag属性是PLACEMENT，就是添加，那么和之前一样，添加到父节点的dom上
  if (fiber.effectTag === 'PLACEMENT' && fiber.dom !== null) {
    domParent.appendChild(fiber.dom);
  } else if (fiber.effectTag === 'UPDATE' && fiber.dom !== null) {
    //如果是Update，那么应该更新它
    //新建一个updateDom函数，用于更新节点
    //更新节点需要三个参数,需要更新的dom,旧属性，新属性
    updateDom(
      fiber.dom,
      fiber.alternate.props,
      fiber.props
    );
  } else if (effectTag === 'DELETION') {
    //删除
    domParent.removeChild(fiber.dom);
  }

  commitWork(fiber.child);
  commitWork(fiber.sibling);
}
```

**现在我们已经能够删除、添加dom，但是updateDom函数还没有完成，所以我们应该处理以下它**
此外我们需要一些额外的方法来帮助我们过滤属性，比如过滤children字段，过滤是否是事件监听器等

**辅助函数如下**
- 过滤非事件监听器(判断是否以on开头)

```js
const isEvent = key => key.startsWith('on');
```

- 用于过滤children与事件监听器

```js
const isProperty = key => key !== 'children' && !isEvent(key);
```

- 过滤新旧属性相等的
```js
const isNew = (prev, next) => key => prev[key] !== next[key];
```

- 过滤掉属性新属性的属性
```js
const isGone = (prev, next) => key => !(key in next);
```

```js
//需要更新的dom,旧属性，新属性
function updateDom(dom, prevProps, nextProps) {
  //移除旧的或改变了的事件监听器(event listeners)
  Object.keys(prevProps)
    .filter(isEvent)//先过滤掉非事件监听器
    .filter(//然后过滤掉事件不变的,或者不再新属性中的(旧的)
      key => !(key in nextProps) || isNew(prevProps, nextProps)(key)
    )
    .forEach(name => {
      //转成小写且去掉on
      const eventType = name.toLowerCase().substring(2);
      //移除旧的或改变了的事件监听器
      dom.removeEventListener(eventType, prevProps[name]);
    })；
  //添加新的事件监听器
  Object.keys(nextProps)
    .filter(isEvent)//过滤非事件监听
    .filter(isNew(prevProps, nextProps))//过滤不变的
    .forEach(name => {
      const eventType = name.toLowerCase().substring(2);
      //新增事件监听器
      dom.addEventListener(eventType, nextProps[name]);
    });
  
  //移除旧属性
  Object.keys(prevPorps)
    .filter(isPropperty)//过滤children属性
    .filter(isGone(prevProps, nextProps))//过滤新属性中的属性
    .forEach(name => {
      dom[name] = '';//移除
    });
  //设置新属性
  Object.keys(nextProps)
    .filter(isProperty)
    .filter(isNew(prevProps, nextProps))
    .forEach(name => {
      //设置新属性
      dom[name] = nextProps[name];
    })
}
```

**这边还有一个地方需要修改的是，我们之前使用createDom建立dom节点时，只是把属性添加上去，并没有判断是否是事件监听器，所以，我们需要修改一下**
```js
function createDom(fiber) {
  const dom = 
    fiber.type === 'TEXT_ELEMENT'
      ? document.createTextNode('')
      : document.createElement(fiber.type);
  //新增(直接调用updateDom,需要三个入参当前节点、旧属性(没有旧属性，因为是新创建的)、新属性)
  updateDom(dom, {}, fiber.props);

  return dom;
}
```


