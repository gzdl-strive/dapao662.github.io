---
title: 简易Vuex
date: 2021-09-10 16:48:24
toc: true
---

## 先简单了解下Vuex的使用
根目录下的store文件夹,index.js文件
```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    counter: 0
  },
  mutations: {
    add(state) {
      state.counter++;
    }
  },
  actions: {
    add({ commit }) {
      setTimeout(() => {
        commit('add');
      }, 1000);
    }
  },
  getters: {
    doubleCounter(state) {
      return state.counter * 2;
    }
  }
})
```

**main.js文件引入**
```js
import stroe from './store';
//...

new Vue({
  router,//路由配置项
  store,
  render: h => h(App)
}).$mount('#app')
```

**然后我们就可以在任意组件中使用$store来访问store中的内容**
* $store.commit方法会触发mutations方法
* $store.dispatch方法会触发actions方法
```html
<!-- HelloWorld.vue -->
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <!-- 使用的是mutations方法，commit方法会触发mutations -->
    <p @click="$store.commit('add')">counter: {{ $store.state.counter }}</p>
    <!-- 异步的方法，使用actions方法，通过dispatch方法触发actions -->
    <p @click="$store.dispatch('add')">Async counter: {{ $store.state.counter }}</p>
    <p>{{$store.getters.doubleCounter}}</p>
  </div>
</template>
```

## 分析
>首先是store/index.js文件

```js
Vue.use(Vuex);

export default new Vuex.Store({
  //...
})
```
和vue-router一样，以**插件的形式引入vuex**,而且页面中可以使用this.$store来访问store中的数据，所以vuex也会挂载一个**全局的属性$store**

而且这里实例化的是Vuex下的Store方法，所以我们的vuex导出的应该是一个对象，这个对象包含install方法和Store类

>其实是在使用时，通过$store.commit和$store.dispatch触发mutations和actions中的方法，所以我们应该实现一个commit方法和dispatch方法

>最后是getters方法

## 实现
### 1、初步实现
自定义一个kvuex.js文件
我们先把主体框架弄好
* 插件install方法
* 挂载$store
```js
//kvuex.js文件
let Vue;//保存构造函数

class Store {}

function install(_Vue) {
  Vue = _Vue;//install方法会传递Vue构造函数

  //使用全局混入
  Vue.mixin({
    beforeCreate() {
      if (this.$options.store) {
        Vue.prototype.$store = this.$options.store;
      }
    }
  })
}

export default {
  Store, 
  install
}
```

### 2、实现commit方法，用于触发mutations,实现dispatch方法，用于触发actions
* commit和dispatch方法接受两个参数
 * 第一个参数是：type,表示对应的mutations/actions的函数名
 * 第二个参数是：payload,代表对于函数的入参
```js
class Store {
  constructor(options) {
    this._mutations = options.mutations;//保存mutations
    this._actions = options.actions;//保存actions

    this.commit = this.commit.bind(this);
    this.dispatch = this.dispatch.bind(this);//绑定this指向
  }

  commit(type, payload) {
    const entry = this._mutations[type];//找到对应函数
    if (!entry) {
      console.error('unknow mutations type');
      return;
    }
    //之前我们的方法中都传入一个state,就是在这边处理的，会关联到state
    entry(this.state, payload);
  }

  dispatch(type, payload) {
    const entry = this._actions[type];
    if (!entry) {
      console.error('unknow action type');
      return;
    }
    entry(this, payload);//this指向错乱了，所以需要改变
    //方法一：使用箭头函数
    //方法二：和React中一样，bind(this)
  }
}
```

### 3、getters,及完整代码
```js
class Store {
  constructor(options) {
    //data响应式处理
    /*
    new Vue({
      data 
    })  -->Vue会递归把data中的数据变成响应式的
    */
    //这样会直接把Vue实例暴露给外界，所以Vue源码中代理了一下
    // this.state = new Vue({
    //   data: options.state
    // });

    // this.getters = {};
    //defineProperty(this.getters, 'doubleCounter', {get(){}})
    //最后还能利用vue计算属性做缓存
    this._wrappedGetters = options.getters;//之前定义的getters

    //定义computed选项
    const computed = {};//计算属性
    this.getters = {};
    //{doubleCounter(state){}}
    const store = this;
    Object.keys(this._wrappedGetters).forEach(key => {
      //获取用户定义的getter
      const fn = store._wrappedGetters[key];//douCounter(state) ->接受一个参数
      //但是我们需要把它转成computed(无参数形式)
      computed[key] = function () {
        return fn(store.state);
      }

      //因为getters是只读的，所以我们需要定义一下
      Object.defineProperty(store.getters, key, {
        // _vm上没有key,为什么可以用，我们把computed里面的属性都定义在了_vm上
        get: () => store._vm[key]
      })
    })

    //处理过
    this._vm = new Vue({
      data: {
        $$state: options.state
      },
      computed
    })

    this._mutations = options.mutations;
    this._actions = options.actions;

    this.commit = this.commit.bind(this);
    this.dispatch = this.dispatch.bind(this);
  }
  //包装一下，让用户访问到响应式的值
  get state() {
    //_data是Vue内部定义的，和$data是一样的
    return this._vm._data.$$state;
  }

  set state(v) {
    console.error('please use replaceState to reset state');
  }

  commit(type, payload) {
    const entry = this._mutations[type];
    if (!entry) {
      console.error('unknow mutations type');
    } 
    entry(this.state, payload);
  }

  dispatch(type, payload) {
    const entry = this._actions[type];
    if (!entry) {
      console.error('unknow action type');
    }
    entry(this, payload);//this指向错乱了，所以需要改变
    //方法一：使用箭头函数
    //方法二：和React中一样，bind(this)
  }
}
```
