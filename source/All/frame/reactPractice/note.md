---
title: react笔记
date: 2021-07-19 16:30:24
toc: true
---

## React导出导入语法
>`import React`的导出语法写法是`export default`，`import { Component }`的导出语法写法是`export {Component}`
为什两者可以一起使用呢?

导出写法如下：
```js
export {
  React as default
  Component
}
```

## React插槽功能
**使用props.children来实现**
```js
//Parent.jsx
<Child name="Parent-Child">
  <a href="http://www.baidu.com">百度</a>
</Child>
//Child.jsx
<div>
  {props.children}
</div>
```

## 状态提升
在两个Input组件中，它们各自的内容保存在各自的state当中，要怎么做才能使两个组件共享数据呢?
>答案是状态提升，即将两个组件需要共享的数据保存在共同的父组件中，然后子组件通过props获取父组件数据

## 受控组件和非受控组件
>- 在HTML中，标签`<input>、<textarea>、<select>`的值的改变通常是根据用户输入进行更新。在react中，可变状态通常保存在组件的状态属性中，并且只能使用setState()更新，而呈现表单的React组件也控制着在后续用户输入时该表单中发生的情况，以这种由React控制的输入表单元素而改变其值的方式，称为**受控组件**.
>- 表单数据由DOM本身处理。即不受setState()控制，与传统的HTML表单输入相似，input输入值即显示最新值。**使用ref从DOM获取表单值**

## 类组件事件处理
### 事件绑定this?
- 在DOM元素上直接使用
```js
<div onClick={this.handleClick.bind(this)}></div>
```
该方法缺点：每次点击都调用一次

**优化：在constructor中定义**
```js
constructor(props) {
  super(props);
  this.handleClick = this.handleClick.bind(this)
}
```
优点：只调用一次

### 点击事件
>点击事件最好使用柯里化函数

**不传参情况没有什么问题**
```js
handleClick() {
  console.log('xxx');
}
<div onClick={this.handleClick}></div>
```

**传参时：函数会直接被调用**
```js
handleClick(val) {
  console.log(val);
}
<div onClick={this.handleClick('aaa')}></div>
```
函数会立即执行，不点击也会执行

**优化**
```js
handleClick(val){
  console.log(val);
}
<div onClick={('aaa') => this.handleClick('aaa')}></div>
```
onClick后是一个柯里化的函数，返回handleClick

**进一步优化**
```js
handleClick = (args) => {
  return function (e) {
    console.log(args, e)
  }
}
<div onClick={this.handleClick('hello')}></div>
```

## 类组件的生命周期
**三个阶段**
>1. Mounting
>2. Updation
>3. Unmounting
**父组件render,子组件肯定也render**
**`shouldComponentUpdate(nextProps, nextState)`在子组件中，子组件首次渲染不调用该生命周期函数，该函数返回值为`boolean`类型，如果返回true则会重新render，如果返回false则不会render**

### Component和PureComponent区别
>`PureComponent`其实就是一个继承自`Component`的子类，会自动加载`shouldComponentUpdate`函数。当组件需要更新的时候，`shouldComponentUpdate`会对组件的props和state进行一次浅比较。如果props和state都没有发生变化，那么render方法也就不会出发，当然也就省去了之后的虚拟dom的生成和对比，在react性能方面得到了优化。

### 新生命周期
`getDerivedStateFromProps` 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。

`getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期方法的任何返回值将作为参数传递给 `componentDidUpdate()`。

## 组件间通信
>1. 父->子组件(props或this.props)
>2. 子->父组件(通过属性方法)
>3. 兄弟组件通信(通过状态提升到同一父组件)
>4. 如果传递的参数需要经过多个子组件(使用context)
>5. 状态管理

## Context
使用`react`的`createContext`创建
```js
import { createContext } from 'react';
const testContext = createContext();
const { Provider, Consumer } = testContext;
export {
  testContext,
  Provider,
  Consumer
}
```
**使用**
```js
<Provider value="hello">
  <ChildA />
  <ChildB />
</Provider>
```
**怎么接收呢？(类组件和函数组件)**
**类组件**
```js
class ChildA extends Component {
  static contextType = testContext;
  render() {
    console.log(this);
    return (
      <div>ChildA-{this.context}</div>
    )
  }
}
```

**函数组件**
```js
<Consumer>
  {
    (value) => {
      console.log(value)
      return <div>{value}</div>
    }
  }
</Consumer>
```
>**context例子：可以封装一个context组件，类似于状态管理，统一存放变量**

## HOC(高阶组件)
>`Higher-Order Components`就是一个函数，传给它一个组件，它返回一个新的组件
```js
const NewComponent = higherOrderComponent(YourComponent);
```
例子：该高阶组件(hoc.js)接收一个组件并返回一个新的组件，该新的组件增强了新的功能(这边只是多增加了一个属性)
```js
//hoc.js
import React, { Component } from 'react';
const hoc = (Comp) => {
  return class extends Component {
    render() {
      return <Comp title="hello" {...this.props}></Comp>
    }
  }
}
export default hoc;
```
**如何使用**
```js
//App.jsx
import hoc from './hoc.js';
class App extends Component {
  //...
}
export default hoc(App);
```
**导出的就是增强后的新组件**

## 高阶组件更优雅的写法：修饰器(decorators)
1. 安装babel插件`yarn add @babel/core @babel/plugin-proposal-decorators @babel-preset`
2. 在根目录下创建`.babelrc`文件
```js
//.babelrc
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [
    [
      "@babel/plugin-proposal-decorators",
      {
        "legacy": true
      }
    ]
  ]
}
```
3. 安装插件`yarn add customize-cra react-app-rewired`
4. 在根目录下创建`config-overriders.js`文件
```js
const path = require('path');
const { override, addDecoratorsLegacy } = require('customize-cra');
function resolve(dir) {
  return path.join(__dirname, dir);
}
const customize = () => (config, env) => {
  config.resolve.alias['@'] = resolve('src')
  if (env === 'production') {
    config.externals = {
      'react': 'React',
      'react-dom': 'ReactDOM'
    }
  }
  return config
}
module.exports = override(addDecoratorsLegacy(), customize())
```
**如何使用**
```js
//App.jsx
import hoc from './hoc.js';
@hoc
class App extends Component {
  //...
}
```

## Protal
>Portal提供了一种将子节点渲染到存在于父组件以外的DOM节点的优秀的方案
```js
ReactDOM.createPortal(child, container)
```
第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 fragment。第二个参数（container）是一个 DOM 元素。

### 用法
通常来讲，当你从组件的 render 方法返回一个元素时，该元素将被挂载到 DOM 节点中离其最近的父节点：
```js
render() {
  // React 挂载了一个新的 div，并且把子元素渲染其中
  return (
    <div>
      {this.props.children}
    </div>
  );
}
```
然而，有时候将子元素插入到 DOM 节点中的不同位置也是有好处的：
```js
render() {
  // React 并*没有*创建一个新的 div。它只是把子元素渲染到 `domNode` 中。
  // `domNode` 是一个可以在任何位置的有效 DOM 节点。
  return ReactDOM.createPortal(
    this.props.children,
    domNode
  );
}
```
一个 portal 的典型用例是当父组件有 overflow: hidden 或 z-index 样式时，但你需要子组件能够在视觉上“跳出”其容器。例如，对话框、悬浮卡以及提示框