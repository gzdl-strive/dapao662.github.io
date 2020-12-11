---
title: 初步实现ReactDom.render
date: 2020-12-11 17:23:10
tag: react
toc: true
---

## index.js文件
```js
import ReactDOM from './kreact/reactDom';
import Component from './kreact/component';
import './index.css';

function FunctionComponent(props) {
    return (
        <div className="border">
            <h1>我是函数组件</h1>
            <p>{props.name}</p>
        </div>
    )
}
class ClassComponent extends Component {
  render() {
    return (
      <div className="border">
        <h1>我是类组件</h1>
        <p>{this.props.name}</p>
      </div>
    )
  }
}

function ReactFragment(props) {
  return (
    <>
      <li>1</li>
      <li>2</li>
    </>
  )
}
const jsx = (
  <section className="border">
    <h1>hello</h1>
    <h1>react</h1>
    <a href="http://www.baidu.com">百度</a>
    <FunctionComponent name="函数组件props" />
    <ClassComponent name="类组件props" />
    <>
      <li>hello</li>
    </>
    <ul>
      <ReactFragment />
    </ul>
    {/* {[1, 2].map(item => (
      <ReactFragment key={Math.random()}>
        <h1>1-{item}</h1>
        <h2>2-{item}</h2>
      </ReactFragment>
    ))} */}
  </section>
)

ReactDOM.render(
  jsx, document.getElementById('root')
)

// ! ReactDOM.render与类组件中的render没有关系

// * 不同节点的渲染
// 原生标签节点，比如div、a等  document.createElement
// 文本节点    document.createTextNode或者node.textContext或者是node.nodeValue
// 函数组件     执行函数的结果
// 类组件      先实例化，再执行render函数
// Fragment   直接遍历子节点
```

## ./kreact/reactDom.js
```js
// vnode 虚拟dom节点
// node dom节点

// * vnode

// type 原生标签 string
//      文本节点 没有type
//      函数组件 函数
//      类组件   类
// props 属性 如className 、 href、 id、children等
//

function render(vnode, container) {
    console.log('vnode:', vnode);
    //vnode -> node
    const node = createNode(vnode);
    //添加到容器上
    container.appendChild(node);
}

//文本标签节点
function isStringOrNumber(sth) {
    return typeof sth === 'string' || typeof sth === 'number'
}

// 根据虚拟dom节点，生成真实dom节点
// 这里需要注意的就是节点类型，因为不同节点渲染方式不同
function createNode(vnode) {
    let node;
    const { type } = vnode;

    if (typeof type === 'string') {
        //原生标签类型
        node = updateHostComponent(vnode);
    } else if (isStringOrNumber(vnode)) {
        //文本标签
        node = updateTextComponent(vnode);
    } else if (typeof type === 'function') {
        //处理类组件和函数组件
        node = type.prototype.isReactComponent
            ? updateClassComponent(vnode)
            : updateFunctionComponent(vnode);
    } else {
        // 处理fragment组件
        node = updateFragmentComponent(vnode);
    }

    return node;
}

//原生标签类型
function updateHostComponent(vnode) {
    const { type, props } = vnode;
    const node = document.createElement(type); //真实dom节点
    updateNode(node, props);
    // vnode->node, 再把node插入到parentNode
    reconcilChildren(node, props.children);
    return node;
}

// 更新真实dom节点属性，className、id、href
// 源码中需要处理的就复杂了，比如style、合成事件
function updateNode(node, props) {
    Object.keys(props)
        .filter(k => k !== 'children')
        .forEach(k => {
            node[k] = props[k]
        });
}

//文本标签类型
function updateTextComponent(vnode) {
    const node = document.createTextNode(vnode);
    return node;
}

//函数组件类型
function updateFunctionComponent(vnode) {
    const { type, props } = vnode;
    const child = type(props);
    //vnode -> node
    const node = createNode(child);
    return node;
}

//类组件类型
// 返回node
// 先实例化
// 再执行render函数
function updateClassComponent(vnode) {
    const { type, props } = vnode;
    const instance = new type(props);
    const child = instance.render();
    //vnode -> node
    const node = createNode(child);
    return node;
}

//Fragment组件
function updateFragmentComponent(vnode) {
    // ! 源码当中并没有使用createDocumentFragment，而是直接处理子节点
    const node = document.createDocumentFragment();
    reconcilChildren(node, vnode.props.children);
    return node;
}

//遍历子节点，子节点是vnode，然后再vnode->node,再插入parentNode中
// 遍历执行render
function reconcilChildren(parentNode, children) {
    const nodeChildren = Array.isArray(children) ? children : [children];
    for (let i = 0; i < nodeChildren.length; i++) {
        let child = nodeChildren[i];
        render(child, parentNode);
    }
}

export default { render }
```

## ./kreact/component.js
```js
export default function Component(props) {
    this.props = props;
}

Component.prototype.isReactComponent = {};
```

## ./index.css
```css
.border {
    margin: 10px;
    padding: 10px;
    border: solid 1px red;
  }
```
