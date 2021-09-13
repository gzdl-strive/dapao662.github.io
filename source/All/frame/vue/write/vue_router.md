---
title: 简易Router
date: 2021-09-10 16:48:02
toc: true
---

## 基础用法
先了解下router的用法
**Vue项目，router文件夹下的index.js文件**
我们忽略其他内容，只关注如下代码
```js
import Vue from 'vue';//导入Vue
import VueRouter from 'vue-router';//导入VueRouter

Vue.use(VueRouter);

//路由配置表
const routes = [
  {
    path: '/',//地址
    name: 'Home',
    component: Home//配置对象
  },
  {
    path: '/about',
    name: 'About',
    component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  }
]

//2. 创建实例 导出VueRouter的实例
const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router
```

**根目录下的main.js文件**
```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

Vue.config.productionTip = false

new Vue({
  router,//添加到配置项中
  store,
  render: h => h(App)
}).$mount('#app')
```

**然后我们就可以在项目的任何地方使用**
```html
<template>
  <div id="app">
    <div id="nav">
      <!-- 4. 导出了两个组件 -->
      <!-- 不用声明就可以直接使用：全局注册了 -->
      <!-- 路由跳转链接 -->
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <!-- 路由出口 -->
    <!-- 为什么点击链接就会改变 -->
    <!-- 利用vue响应式：current -->
    <router-view/>
  </div>
</template>
```

## 分析路由功能
**首先：router/index.js中如下代码**
```js
Vue.use(VueRouter);
```
>从Vue.js官网可以得知：使用Vue.use的用法是安装Vue.js插件。如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。

所以我们可以得出以下结论

**`VueRouter`是一个插件:因为使用了`Vue.use`**

内部做了什么??
1. 实现并声明了组件 `router-view` `router-link` 全局注册了
2. 是一个插件，所以会有install方法 `this.$router.push()`方法

**然后：main.js中把router/index.js中导出的VueRouter实例添加的Vue构造函数上**
```js
new Vue({
  router,//添加到配置项中
  store,
  render: h => h(App)
}).$mount('#app')
```
**这样，我们后面就可以使用Vue提供的$options方法获取到该router**


**最后，在页面上使用router-link和router-view组件**
```html
<router-link to="/">Home</router-link>
<router-link to="/about">About</router-link>
<router-view />
```
* 没有声明就可以直接使用，说明全局注册了
* 为什么点击router-link，router-view内容发生改变，--》利用Vue响应式


## 实现简易Router
因为VueRouter是一个插件，必须含有install方法
install方法接受一个入参，该入参为Vue构造函数

### 1、首先创建VueRouter类，并添加install方法
```js
let Vue;//该变量用于保存Vue构造函数，插件中使用，不导入还能用
class VueRouter {}
VueRouter.install = function (_Vue) {
  Vue = _Vue;//我们之所以使用一个变量来存的意义在于：不想打包时一起把Vue构造函数打包进去
}
```

### 2、然后我们应该挂载一个$router属性
这样就可以在全局范围内使用this.$router来访问路由了
这里我们使用全局混入-》为什么?
>在`router/index.js`中，`Vue.use(VueRouter)`很早就定义了，而我们的配置项routes则是在后面定义的，所以此时我们拿不到routes配置项
>这里需要用到全局混入--》**目的：延迟下面逻辑到router创建完毕并且附加到选项上才执行**
```js
VueRouter.install = function (_Vue) {
  Vue = _Vue;

  //1、挂载$router属性
  Vue.mixin({
    beforeCreate() {
      //此钩子在每个组件创建实例时都会调用(包括根组件)
      //根实例才有该选项， main.js中调用了new Vue(), 并且含有我们传入的router配置项
      // console.log(this.$options);
      if (this.$options.router) {
        Vue.prototype.$router = this.$options.router;//相当于全局注册了，因为每个Vue创建的实例都可以访问到$router了
      }
    }
  })
}
```

### 3、接着注册并实现router-link组件
>注意：在使用`router-link`时会传入一个属性to，所以我们可以使用props来获取
>`router-link`标签内部动态内容可以使用插槽来获取，`this.$slots.default`
```js
VueRouter.install = function (_Vue) {
  Vue = _Vue;
  
  //1、挂载$router属性
  Vue.mixin({
    //...
  })

  //2、注册并实现router-link
  Vue.component('router-link', {
    props: {
      to: {
        type: String,//其实还可以传入对象，这边简便行事
        required: true
      },
    },
    render(h) {
      //渲染函数，也可以使用jsx来
      //<a href="to">xxx</a>
      return h('a', {
        attrs: {
          href: '#' + this.to//使用hash方法可以不跳转页面
        }
      }, this.$slots.default)
    }
  })
}
```

**我们还需要处理的是router-view组件，但是我们先在VueRouter构造器中初始化一些变量**
因为我们使用router-view时会匹配到路径对应的组件
```js
//实例化时传入一个对象
/*
{
  mode: 'hash',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',//地址
      name: 'Home',
      component: Home//配置对象
    },
    {
      path: '/about',
      name: 'About',
      component: () => import('../views/About.vue')
    }
  ]
}
*/
class VueRouter {
  constructor(options) {
    this.$options = options; //定义一个$options用于获取配置项
    // this.current = "/";//因为current不是一个响应式数据，所以我们切换路由current改变了，router-view的render函数不会再次调用
    //所以需要把current变成一个响应式数据
    const initial = window.location.hash.slice(1) || "/";
    //使用defineReactive定义响应式数据
    Vue.util.defineReactive(this, 'current', initial);

    //监听hash变化
    window.addEventListener('hashchange', () => {
      this.current = window.location.hash.slice(1);
    })
  }
}
```

现在我们已经定义了一个current的响应式数据，并且监听了路由的变化，所以，我们现在来编写router-view组件来渲染对象的内容

**注册并实现router-view**
```js
VueRouter.install = function (_Vue) {
  //...

  Vue.component('router-view', {
    render(h) {
      //获取当前路由所对应的组件，然后放入h函数中渲染出来
      //h函数中可以直接放一个组件

      // *  因为我们之前在Install方法中挂载过$router方法，使得每个组件都能调用$router方法
      let component = null;
      //this.$router就是我们new VueRouter({route})的结果
      //$options和current都是我们在构造器中定义的变量，current用于表示当前路由,$options用于保存配置项
      const route = this.$router.$options.routes.find(
        route => route.path === this.$router.current//匹配当前路由
      )

      if (route) {
        component = route.component;
      }
      return h(component);//直接渲染组件
    }
  })
}
```


