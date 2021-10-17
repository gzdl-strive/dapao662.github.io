---
title: 路由的基本使用
toc: true
date: 2021-10-15 10:56:02
---

1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。
2. 前端路由：key是路径，value是组件。

### 1.基本使用

- 1、安装vue-router，命令：```npm i vue-router```

- 2、应用插件：```Vue.use(VueRouter)```

- 3、编写router配置项
```js
//引入VueRouter
import VueRouter from 'vue-router'
//引入路由组件
import About from '../components/About';
import Home from '../components/Home';

//创建router实例对象，去管理一组一组的路由规则
const router = new VueRouter({
  routes: [
    {
      path: '/about',
      component: About,
    },
    {
      path: '/home',
      component: Home
    }
  ]
})
//暴露router
export default router
```

- 4、使用路由器
```js
//main.js
//...
import VueRouter from 'VueRouter';
import router from './router';

//引用VueRouter插件
Vue.use(VueRouter);

//添加路由器到配置项中
new Vue({
  //...
  router,
  //...
})
```

- 5、实现切换(active-class可配置高亮样式)
```html
<router-link active-class="active" to="/about">About</router-link>
```

- 6、指定展示位置
```html
<router-view></router-view>
```

### 2. 几个注意点
1. 路由组件通常存放在```pages```文件夹，一般组件通常存放在```components```文件夹
2. 通过切换，“隐藏”了路由组件，默认是被销毁掉的，需要的时候再去挂载
3. 每个组件都有自己的```$route```属性，里面存储着自己的路由信息
4. 整个应用只有一个router,可以通过组件的```$router```属性获取到



