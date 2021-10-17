---
title: 缓存路由组件
date: 2021-10-17 13:00:24
toc: true
---

## 缓存路由组件
>作用：让不展示的路由组件保持挂载，不被销毁

**具体编码**
```html
<keep-alive include="News">
  <router-view></router-view>
</keep-alive>
```

## 两个新的生命周期钩子
1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。
2. 具体名字：
   1. ```activated```路由组件被激活时触发。
   2. ```deactivated```路由组件失活时触发。

**之前如下代码的逻辑都是在Mounted中和beforeDestroy钩子中执行的，但是我们的缓存组件不存在销毁这么一说，所以beforeDestroy不生效，需要使用缓存组件特有的生命周期钩子来执行逻辑**
```js
export default {
  //...
  activated() {
    console.log('News组件被激活了');
    this.timer = setInterval(() => {
      console.log('@');
      this.opacity -= 0.01;
      if (this.opacity <= 0>) {
        this.opacity = 1;
      }
    }, 16)
  },
  deactivated() {
    console.log('News组件失活了');
    clearInterval(this.timer);
  }
  //...
}
```