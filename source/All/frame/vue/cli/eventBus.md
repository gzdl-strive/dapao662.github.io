---
title: 全局事件总线
toc: true
date: 2021-10-14 11:18:24
---

## 全局事件总线(GlobalEventBus)

>- 一种组件间通信的方式，适用于<span style="color: red">任意组件间通信</span>

>- 安装全局事件总线：
```js
new Vue({
  ...
  beforeCreate() {
    Vue.prototype.$bus = this;
  }
  ...
})
```

## 使用事件总线
- 接收数据：A组件想接收数据，则在A组件中给$bus绑定自定义事件，事件的<span style="color:red">回调留在A组件自身。</span>
```js
methods() {
  demo(data) {....}
},
mounted() {
  this.$bus.$on('xxxx', this.demo);
}
```

- 提供数据：`this.$bus.$emit('xxxx', 数据)`

## 解绑自定义事件
>最好在beforeDestroy钩子中，使用$off取解绑<span style="color:red">当前组件所用到的</span>事件。

```js
//main.js
new Vue({
  el: '#app',
  render: h => h(App),
  beforeCreate() {
    Vue.prototype.$bus = this;
  }
})
````