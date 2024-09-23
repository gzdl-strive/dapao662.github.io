---
title: React15和React16生命周期对比，初识Fiber
date: 2020-11-17 19:58:10
tag: react
toc: true
---

**React的生命周期就是组件从初始化到卸载到全过程，可以分为以下几个阶段**
1. 初始化阶段
2. 更新阶段
3. 卸载阶段
>为了更加了解React生命周期的发展背景，我们先从React15来看一下React的生命周期

## React15的生命周期
![1](/assets/reactImg/3-1.png "react15生命周期")

### 初始化阶段
在初始化阶段，生命周期会按照如下流程执行，这个过程在组件的一生中只会执行一次
![2](/assets/reactImg/3-2.png "react15初始化")
**constructor**: 常用来初始化`State`
**componentWillMount**: 通过render函数生成虚拟DOM之前触发，鸡肋
**render**: 通过Diff算法，生成新的虚拟DOM
**componentDidMount**:组件渲染完成之后触发，这里可以操作真实DOM,也可以进行异步数据请求

### 更新阶段
**更新有两种情况：**
1. 父组件更新触发的更新
2. 自身State改变触发的更新
![3](/assets/reactImg/3-3.png "更新阶段")
**注：`componentWillReceiveProps()`是由父组件的更新触发的，跟`props`是否变化无关**
>这里重点了解一下`shouldComponentUpdate(nextProps,nextState)`,这是React提供的一个用于性能优化的一个点
默认情况下，每次数据更新React都会去频繁调用render函数，而render函数是用来生成新的虚拟DOM的，这个过程比较耗时，`shouldComponentUpdate()`方法在render函数之前执行，它会返回一个`boolean`类型的值（默认为true），当返回true当时候，才回继续走后续的更新生命周期流程，否则不会继续执行
举个🌰
```js
shouldComponentUpdate(nextProps,nextState){
  if(...){
     return true
  }else{
    return false
  }
}
```

### 卸载阶段
组件卸载掉时候，会执行`componentWillUnmount()`方法

### 总结
>`React15`中生命周期可以分为三个阶段，分别是**初始化阶段**，**更新阶段**，**卸载阶段**
**初始化阶段**：`constructor`, `componentWillMount`, `render`, `componentDidMount`
**更新阶段**： `componentWillReceiveProps`, `shouldComponentUpdate`, `componentWillUpdate`, `render`, `componentDidUpdate`
**卸载阶段**： `componentWillUnmont`
这里需要注意的是更新阶段，`componentWillReceiveProps`是由父组件触发的更新，只要父组件更新，子组件的该生命周期就会被执行，跟`props`无关。同是，`shouldComponentUpdate`可以用来做性能优化

## React16的生命周期
先来大概看一下React16里面的生命周期图
![4](/assets/reactImg/3-4.png "react16生命周期")
在React16的生命周期中，去掉了曾经的`componentWillMount`和`componentWillUpdate`，使用`getDerivedStateFromProps`代替这两个方法。同时在更新阶段的`render`方法和`componentDidUpdate`之间，新增了一个`getSnapshotBeforeUpdate`方法。接下来就来详细了解一下React这么做的原理以及这些新增生命周期的具体使用
举个🌰
**父组件**
```js
import React, { Component } from "react";
import SonComponent from "./test2";

class FatherComponent extends Component {
    constructor(props) {
        super(props);
        this.state = {
            fatherTxt: '父组件生命周期测试',
            sonTxt: "子组件接受父组件的参数"
        };
        console.log('父组件constructor...');
    }
    static getDerivedStateFromProps() {
        console.log('父组件getDerivedStateFromProps...');
        return null;
    }
    componentDidMount() {
        console.log('父组件componentDidMount...');
    }
    componentDidUpdate() {
        console.log('父组件componentDidUpdate...');
    }
    getSnapshotBeforeUpdate() {
        console.log('父组件getSnapshotBeforeUpdate...');
        return null;
    }
    changeFatherTxt() {
        this.setState({
            fatherTxt: "父组件改变之后的文案"
        });
    }
    changeSonTxt() {
        this.setState({
            sonTxt: "子组件改变之后的文案"
        });
    }
    render() {
        console.log('父组件render...');
        return (
            <div>
                <p>父组件文案:{this.state.fatherTxt}</p>
                <button onClick={this.changeFatherTxt.bind(this)}>父组件改变State</button>
                <button onClick={this.changeSonTxt.bind(this)}>改变子组件State</button>
                <hr/>
                <SonComponent sonTxt={this.state.sonTxt}/>
            </div>
        );
    }
}
export default FatherComponent;
```
**子组件**
```js
import React, { Component } from "react";

class SonComponent extends Component {
    constructor(props) {
        super(props);
        this.state = {};
        console.log('子组件constructor...');
    }
    static getDerivedStateFromProps() {
        console.log('子组件getDerivedStateFromProps...');
        return null;
    }
    componentDidMount() {
        console.log('子组件componentDidMount...');
    }
    componentDidUpdate() {
        console.log('子组件componentDidUpdate...');
    }
    getSnapshotBeforeUpdate() {
        console.log('子组件getSnapshotBeforeUpdate...');
        return null;
    }
    render() {
        console.log('子组件render...');
        return (
            <div>
                <p>子组件文案--{this.props.sonTxt}</p>
            </div>
        )
    }
}

export default SonComponent;
```
**打印示例**
![5](/assets/reactImg/3-5.png "打印实例")
### 初始化阶段（挂载）
为了比较React15和React16的区别，先来看如下一张图
![6](/assets/reactImg/3-6.png "React15和React16的区别")
>可以很直观的发现，比起`React15`，`React16`的生命周期去掉了`componentWillMount`，新增了`getDerivedStateFromProps`。需要注意的是，`getDerivedStateFromProps`并不是用来代替`componentWillMount`方法的，它是用来替换`componentWillReceiveProps`的。
至于`componentWillMount`方法，本身就很鸡肋，而且经常被不恰当的使用，所以在React16中被去掉了

接下来就来重点看一下`getDerivedStateFromProps`这个方法，从名字上就可以看出，该方法是用来**根据父组件传递过来的props来更新自身的state**的一个方法，使用过程中有以下几点需要注意
1. `getDerivedStateFromProps`是一个静态方法，声明的使用`static`
```js
static getDerivedStateFromProps(props,state){
  return newState
}
```
2. `getDerivedStateFromProps`接收两个参数，分别是父组件传递过来的`props`和自身的`state`
示例同1
3. `getDerivedStateFromProps`必须返回一个**对象格式的返回值**，否则控制台会被警告
该返回值会被用来更新现有`state`(并不会覆盖原有`State`，只做定向更新，如果原来`State`中没有该属性，则新增)，如果没有需要更新的时候，请记得返回一个`null`
![7](/assets/reactImg/3-7.png "getDerivedStateFromProps")
### 更新阶段（组件更新）
![8](/assets/reactImg/3-8.png "更新阶段")
在初始化阶段，我们重点了解了一下`getDerivedStateFromprops`方法，上面我们也说过，`getDerivedStateFromProps`是用来替代`componentWillReveiveprops`方法的。根据React官方的描述
>与 `componentDidUpdate` 一起，这个新的生命周期涵盖过时`componentWillReceiveProps` 的所有用例。
**But Why?**
这里引用拉钩教育修言老师在深入浅出搞定React中的讲解，我觉得他这个讲解通俗易懂，实在无法超越
>说回 `getDerivedStateFromProps` 这个 API，它相对于早期的 `componentWillReceiveProps` 来说，正是做了“合理的减法”。而做这个减法的决心之强烈，从 `getDerivedStateFromProps` 直接被定义为 `static` 方法这件事上就可见一斑—— `static` 方法内部拿不到组件实例的 `this`，这就导致你无法在 `getDerivedStateFromProps` 里面做任何类似于 `this.fetch()`、不合理的 `this.setState`（会导致死循环的那种）这类可能会产生副作用的操作。
因此，`getDerivedStateFromProps` 生命周期替代 `componentWillReceiveProps` 的背后，是 `React 16` 在强制推行“只用 `getDerivedStateFromProps` 来完成 `props` 到 `state` 的映射”这一最佳实践。意在确保生命周期函数的行为更加可控可预测，从根源上帮开发者避免不合理的编程方式，避免生命周期的滥用；同时，也是在为新的 `Fiber` 架构铺路。

除了`getDerivedState`方法，在更新阶段，React16中也去掉了`componentWillUpdate`方法，新增了`getSnapshotBrforeUpdate`方法，这个方法在`render`方法之后，`componentDidUpdate`之前被执行，即真实DOM更新之前（获取更新前的真实DOM和更新前后的`State&props`信息）。该方法需要一个返回值，作为`componentDidUpdate`的第三个参数。由于该方法使用场景很少，这里不做详细介绍
### 卸载阶段
同React15
### 总结
相对于`React15`，`React16`的生命周期中去掉了`componentWillMout`和`componentWillUpdate`方法，并且使用`getDerivedStateFromProps`方法替代了之前的`componentWillReceiveprops`，使得React的生命周期更纯粹，只用来做专门的事情，避免大量业务逻辑代码嵌入生命周期，同时也是在为`Fiber`架构铺路

## 初识Fiber架构
**什么是Fiber？Fiber对React的改变？**
`Fiber`是`React16`对`React`核心算法的一次重写
`FIber`会使原本同步渲染过程变成异步
### React历史算法有什么风险？
>在React16之前，每一次组件的更新都会触发React去构建一颗新的虚拟DOM树，通过与上一次虚拟DOM树的diff算法对比，实现对DOM的定向更新。这个过程是一个递归的过程，调用栈非常深，只有最底层的返回了，整个渲染过程才回开始逐层返回。而这个漫长且不可被打断的过程，将会给用户的体验带来巨大的风险：同步渲染一旦开始，便会牢牢抓住线程，直到递归完成，这个国恒浏览器不会处理任何渲染之外的事情，会进入一种无法处理用户交互的状态，页面可能会卡死。
### Fiber是怎样处理渲染的？
>Fiber 会将一个大的更新任务拆解为许多个小任务。每当执行完一个小任务时，渲染线程都会把主线程交回去，看看有没有优先级更高的工作要处理，确保不会出现其他任务被“饿死”的情况，进而避免同步渲染带来的卡顿。在这个过程中，渲染线程不再“一去不回头”，而是可以被打断的，这就是所谓的“异步渲染”
### 说回生命周期
在最开始给出生命周期图的时候，细心的同学会发现，在下面这张图的左边，React又将生命周期划分了如下三个阶段
* render：纯净且没有副作用，可能会被暂停或者终止，重新启动
* Pre-commit阶段：可以读取DOM
* commit阶段：可以使用DOM，运行副作用，安排更新
![9](/assets/reactImg/3-4.png "react16生命周期")
为什么会这样分呢？
>总的来说，`render` 阶段在执行过程中允许被打断，而 `commit` 阶段则总是同步执行的。
为什么这样设计呢？简单来说，由于 `render` 阶段的操作对用户来说其实是“不可见”的，所以就算打断再重启，对用户来说也是零感知。而 `commit` 阶段的操作则涉及真实 `DOM` 的渲染，再狂的框架也不敢在用户眼皮子底下胡乱更改视图，所以这个过程必须用同步渲染来求稳。
### 废除的生命周期跟Fiber之间的联系
>在 `Fiber` 机制下，`render` 阶段是允许暂停、终止和重启的。当一个任务执行到一半被打断后，下一次渲染线程抢回主动权时，这个任务被重启的形式是“重复执行一遍整个任务”而非“接着上次执行到的那行代码往下走”。这就导致 `render` 阶段的生命周期都是有可能被重复执行的。
带着这个结论，我们再来看看 `React 16 `打算废弃的是哪些生命周期：
`componentWillMount`；
`componentWillUpdate`；
`componentWillReceiveProps`。
这些生命周期的共性，就是它们都处于 `render` 阶段，都可能重复被执行，而且由于这些` API` 常年被滥用，它们在重复执行的过程中都存在着不可小觑的风险。



作者：mapengfei47
链接：https://juejin.im/post/6892604247893147656
来源：掘金
