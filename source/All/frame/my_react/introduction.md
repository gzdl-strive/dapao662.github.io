---
title: 绪论
date: 2021-08-13 14:29:02
toc: true
---

>开始之前，先了解以下React、JSX的用法,以及我们需要做的。
```js
import React from 'react';
import ReactDOM from 'react-dom';
const element = <div id="container">Hello</div>;
const container = document.getElementById('root');
ReactDOM.render(element, container);
```
第三行代码是用JSX定义的元素。它不是有效的Javascript代码，所以我们需要使用有效的JS替换它。
JSX通过Babel等构建工具转换为JS.
转换通常很简单：使用`React.createElement`函数转换，该函数接收标签名、属性对象、children作为参数.

`React.createElement`会根据入参创建一个对象。
该对象具有两个属性：type、props(其实它有更多，但是我们只关心这两个)
- type: 是一个字符串，我们要创建的DOM节点的指定类型.（它也可以是一个函数，在后面会用到）
- props: 是另一个对象，它具有来自JSX属性的所有键值。它还有一个特殊的属性：children.
  - children可以是一个字符串，但更多情况下是一个具有更多元素的数组。

```js
const element = <div id="container">
  <h1>Hello</h1>
  <h2>World!</h2>
</div>
```
**以上代码通过Babel转换成**
```js
const element = React.createElement(
  "div", 
  {
    id: "container"
  }, 
  React.createElement("h1", null, "Hello"), 
  React.createElement("h2", null, "World!"));
```
我们在控制台把它打印出来可以看到这样一个对象：
![my-react](/assets/reactImg/my_react_1.png "jsx")

简化完就是这样的一个对象：
```js
{
  type: 'div',
  props: {
    id: 'container',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Hello'
        }
      },
      {
        type: 'h2',
        props: {
          children: 'World!'
        }
      }
    ]
  }
}
```

`render函数`是React更改DOM的地方，所以我们如果自己更新上面的代码的话，大致效果如下：
```js
const element = <h1 title="foo">Hello</h1>
//上面的jsx语法通过createElement语法转换成如下
const element = React.createElement(
  type: 'h1',
  props: {
    title: 'foo',
    children: 'Hello'
  },
)
const container = document.getElementById('root');
// ReactDOM.render(element, container);
//1、首先创建一个节点
const node = document.createElement(element);
//2、为Node节点添加属性
node['title'] = element.props.title;
//3、创建子节点
const text = document.createTextNode('');
//4、为文本节点添加内容
text['nodeValue'] = element.props.children;
//5、提交到容器上
//5.1把文本节点提交到父节点上
node.append(text);
//5.2把element节点提交到根节点上
container.appendChild(node);
```

**上述创建文本节点是，使用`textNode`而不是设置`innerText`，是因为我们以后会以相同的方式处理所有元素。还有注意我们是如何设置的，`nodeValue`就行我们对h1标题所做的那样，他几乎就像字符串有`props: {nodeValue: 'hello'}`**



