---
title: Redux笔记
date: 2021-07-19 17:26:24
toc: true
---


## 前言
>- 用户的使用方式复杂
>- 不同身份的用户有不同的使用方式（比如普通用户和管理>员）
>- 多个用户之间可以协作
>- 与服务器大量交互，或者使用了WebSocket
>- View要从多个来源获取数据
**上面这些情况才是 Redux 的适用场景：多交互、多数据源。**

从组件角度看，如果你的应用有以下场景，可以考虑使用 Redux
>- 某个组件的状态，需要共享
>- 某个状态需要在任何地方都可以拿到
>- 一个组件需要改变全局状态
>- 一个组件需要改变另一个组件的状态

## 设计思想
Redux 的设计思想很简单，就两句话。
>（1）Web 应用是一个状态机，视图与状态是一一对应的。
>（2）所有的状态，保存在一个对象里面。

## 工作流程
![redux-flow](/assets/reactImg/redux_flow.jpg "redux_flow")
首先，用户发出Action。
```js
store.dispatch(action);
```
然后，Store自动调用Reducer，并且传入两个参数(当前State和收到的Action)。Reducer会返回新的State。
```js
let nextState = reducer(previousState, action)
```
State一旦有变化，Store就会调用监听函数
```js
//设置监听函数
store.subscribe(listener);
```
`listener`可以通过`store.getState`得到当前状态。如果使用的是React,这时可以触发重学渲染View.
```js
function listener() {
  let newState = store.getState();
  component.setState(newState);
}
```

## 基本概念和API

### store
>**Store**: 就是保存数据的地方，你可以把它看成一个容器。整个应用只能有一个Store。Redux提供了`createStore`这个函数，用来生成Store。
```js
import { createStore } from 'redux';
const store = createStore(reducer);
```
上面代码中，`createStore`函数接受另一个函数(reducer函数)作为参数，返回新生成的 Store 对象。

### state
>**State**: Store对象包含所有数据。如果想得到某个时点的数据，就要对 Store 生成快照。这种时点的数据集合，就叫做 State。
当前时刻的 State，可以通过`store.getState()`拿到。
```js
import { createStore } from 'redux';
const store = createStore(reducer);
const state = store.getState();
```

### Action
>**Action**： State 的变化，会导致 View 的变化。但是，用户接触不到 State，只能接触到 View。所以，State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发生变化了。
Action 是一个对象。其中的**type**属性是必须的，表示 Action 的名称。
```js
const action = {
  type: 'ADD_TODO',
  payload: 'Learn Redux'
};
```

### Action Creator
>**Action Creator**: View 要发送多少种消息，就会有多少种 Action。如果都手写，会很麻烦。可以**定义一个函数来生成 Action**，这个函数就叫 `Action Creator`。
```js
const ADD_ToDO = '添加TODO';
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
const action = addTodo('Learn Redux')
```
上面代码中，`addTodo`函数就是一个 `Action Creator`。

### Dispatch()
>**store.dispatch()**:`store.dispatch()`是 `View` 发出 `Action` 的唯一方法。
```js
import { createStore } from 'redux';
const store = createStore(fn);
store.dispatch({
  type: 'ADD_TODO',
  payload: 'Learn Redux'
});
```
上面代码中，`store.dispatch`接受一个 `Action` 对象作为参数，将它发送出去。
结合 `Action Creator`，这段代码可以改写如下。
```js
store.dispatch(addTodo('Learn Redux'));
```

### Reducer
Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 **State 的计算过程(修改状态)**就叫做 `Reducer`。
`Reducer` 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。
```js
const reducer = function (state, action) {
  // ...
  return new_state;
};
```
整个应用的初始状态，可以作为 State 的默认值。下面是一个实际的例子。
```js
const defaultState = 0;
const reducer = (state = defaultState, action) => {
  switch (action.type) {
    case 'ADD':
      return state + action.payload;
    default: 
      return state;
  }
};
const state = reducer(1, {
  type: 'ADD',
  payload: 2
});
```
实际应用中，`Reducer 函数`不用像上面这样手动调用，**store.dispatch方法会触发 Reducer 的自动执行。**为此，Store 需要知道 Reducer 函数，做法就是在生成 Store 的时候，**将 Reducer 传入createStore方法**。
```js
import { createStore } from 'redux';
const store = createStore(reducer);
```
上面代码中，`createStore`接受 `Reducer` 作为参数，生成一个新的 Store。以后每当s`tore.dispatch`发送过来一个新的 Action，就会自动调用 Reducer，得到新的 State。

**为什么这个函数叫做 Reducer 呢？因为它可以作为数组的reduce方法的参数**
```js
const actions = [
  { type: 'ADD', payload: 0 },
  { type: 'ADD', payload: 1 },
  { type: 'ADD', payload: 2 }
];
const total = actions.reduce(reducer, 0); // 3
```
上面代码中，数组`actions`表示依次有三个Action,分别是加`0`,加`1`和加`2`。数组的reduce方法接收Reducer函数作为参数，就可以得到最终的状态`3`

### 3.7纯函数
Reducer 函数最重要的特征是，它是一个**纯函数。**也就是说，只要是同样的输入，必定得到同样的输出

纯函数是函数式编程的概念，必须遵守以下一些约束。
>- 不得改写参数
>- 不能调用系统I/O的API
>- 不能调用Date.now()或者Math.random()等不纯的方法，因为每次会得到不一样的结果

由于 Reducer 是纯函数，就可以保证同样的State，必定得到同样的 View。但也正因为这一点，Reducer 函数里面不能改变 State，必须返回一个全新的对象，请参考下面的写法。
```js
//State是一个对象
function reducer(state, action) {
  return Object.assign({}, state, {thingToChange});
  //或者
  return { ...state, ...newState };
}

//State是一个数组
function reducer(state, action) {
  return [...state, newItem]
}
```
最好把 State 对象设成只读。你没法改变它，要得到新的 State，唯一办法就是生成一个新对象。这样的好处是，任何时候，与某个 View 对应的 State 总是一个不变的对象。

### 3.8 stroe.subscribe()
Store允许使用`store.subscirbe`方法设置监听函数，一旦State发生变化，就自动执行这个函数。
```js
import { createStore } from 'redux';
const store = createStore(reducer);
store.subscribe(listener);
```
显然，只要把View的更新函数(对于React项目，就是组件的render方法或setState方法)放入`listener`,就会实现View的自动渲染。
`store.subscribe`方法返回一个函数，调用这个函数就可以解除监听。
```js
let unsubscribe = store.subscribe(() => {
  console.log(store.getState());
});
unsubscribe();
```

## Store的实现
上一节介绍了 Redux 涉及的基本概念，可以发现 Store 提供了三个方法
>- store.getState()
>- store.dispatch()
>- store.subscribe()

```js
import { createStore } from 'redux';
let { subscirbe, dispatch, getState } = createStore(reducer);
```

`createStore`方法还可以接收第二个参数，表示State的最初状态。这通常是服务器给出的。
```js
let store = createStore(reducer, window.STATE_FROM_SERVER);
//window.STATE_FROM_SERVER就是整个应用的状态初始值
```
`window.STATE_FROM_SERVER`就是整个应用的状态初始值,**注意，如果提供了这个参数，它会覆盖 Reducer 函数的默认初始值。**

下面是createStore方法的一个简单实现，可以了解一下Store是怎么生成的
```js
const createStore = (reducer) => {
  let state;//状态
  let listeners = [];//监听函数
  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);//生成新状态
    //发布
    listeners.forEach(listener => listener());
  }

  const subscribe = (listener) => {
    listeners.push(listener);//订阅
    return () => {
      listeners = listeners.filter(fn => fn !== listener)
    }
  }

  dispatch({});//调用createStore时会默认调用依次dispatch方法

  return { getState, dispatch, subscirbe }
}
```

## Reducer的拆分
Reducer 函数负责生成 State。由于整个应用只有一个 State 对象，包含所有数据，对于大型应用来说，这个 State 必然十分庞大，导致 Reducer 函数也十分庞大
```js
const chatReducer = (state = defaultState, action = {}) => {
  const { type, payload } = action;
  switch(type) {
    case ADD_CHAT:
      return {
        ...state, 
        chatLog: state.chatLog.concat(payload)
      };
    case CHANGE_STATUS:
      return {
        ...state,
        statusMessage: payload
      };
    case CHANGE_USERNAME:
      return {
        ...state,
        userName: payload
      };
    default: return state;
  }
}
```
上面代码中，三种 Action 分别改变 State 的三个属性。
- `ADD_CHAT`：chatLog属性
- `CHANGE_STATUS`：statusMessage属性
- `CHANGE_USERNAME`：userName属性

这三个属性之间没有联系，这提示我们可以把 Reducer 函数拆分。不同的函数负责处理不同属性，最终把它们合并成一个大的 Reducer 即可。
```js
const chatReducer = (state = defaultState, action = {}) => {
  return {
    chatLog: chatLog(state.chatLog, action),
    statusMessage: statusMessage(state.statusMessage, action),
    userName: userName(state.userName, action)
  }
};
```
上面代码中，Reducer 函数被拆成了三个小函数，每一个负责生成对应的属性
这样一拆，Reducer 就易读易写多了。而且，这种拆分与 React 应用的结构相吻合：一个 React 根组件由很多子组件构成。这就是说，子组件与子 Reducer 完全可以对应。
Redux 提供了一个`combineReducers`方法，用于 Reducer 的拆分。你只要定义各个子 Reducer 函数，然后用这个方法，将它们合成一个大的 Reducer。
```js
import { combineReducers } from 'redux';
const chatReducer = combineReducers({
  chatLog,
  statusMessage,
  userName
})
export default todoApp;
```
下面是combineReducer的简单实现。
```js
const combineReducers = reducers => {
  return (state = {}, action) => {
    return Object.keys(reducers).reduce(
      (nextState, key) => {
        nextState[key] = reducers[key](state[key], action);
        return nextState;
      },
      {} 
    );
  };
};
```