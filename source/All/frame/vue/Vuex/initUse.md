---
title: 初步使用
date: 2021-10-14 15:10:24
toc: true
---

**Vuex工作图**
![Vuex](/assets/vueImg/vuex.png "Vuex")

### 1、概念
在Vue中实现集中式状态(数据)管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

### 2、何时使用
- 多个组件需要共享数据时

### 3、搭建Vuex环境
1、创建文件：`src/store/index.js`

```js
//引入Vue核心库
import Vue from 'vue';
//引入Vuex
import Vuex from 'vuex';

//引用Vuex插件
Vue.use(Vuex);

//准备actions对象————响应组件中用户的动作
const actions = {};
//准备mutations对象————修改state中的数据
const mutations = {};
//准备state对象————保存具体的数据
const state = {};

//创建并暴露store
export default new Vuex.Store({
  actions,
  mutations,
  state
})
```
2、在`main.js`中创建vm时传入`store`配置项
```js
......
//引入store
import store from './store'
......

//创建vm
new Vue({
  el:'#app',
  render: h => h(App),
  store
})
```

### 4.一个问题
如果我们在`store/index.js`中没有使用`Vue.use(Vuex)`应用Vuex插件，而是在`main.js`中引入store后再应用插件，那么会报错,错误如下：
<span style="color: red">[vuex] must call Vue.use(Vuex) before creating a store instance.[vuex]必须在创建存储实例之前调用Vue.use（vuex）<span>

**但是当我们把import store放在Vue.use(Vuex)后面也不行，因为脚手架会把import都提到前面执行**

**所以我们就应该在创建store时就应用插件**

### 5.基本使用
1. 初始化数据、配置```actions```、配置```mutations```，操作文件```store.js```

```js
//引入Vue核心库
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//引用Vuex
Vue.use(Vuex)

const actions = {
  //响应组件中加的动作
  jia(context, value) {
    context.commit('JIA', value);
  }
}

const mutations = {
  //执行加
  JIA(state,value){
    // console.log('mutations中的JIA被调用了',state,value)
    state.sum += value
  }
}

//初始化数据
const state = {
  sum:0
}

//创建并暴露store
export default new Vuex.Store({
  actions,
  mutations,
  state,
})
```

### 组件中读取vuex中的数据
```html
<template>
  <div>
    <p>{{ $store.state.sum }}</p>
  </div>
</template>
```

### 组件中修改vuex中的数据
```$store.dispatch('action中的方法名',数据)``` 或 ```$store.commit('mutations中的方法名',数据)```

>  备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写```dispatch```，直接编写```commit```

