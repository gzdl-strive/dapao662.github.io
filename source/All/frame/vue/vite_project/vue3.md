---
title: Vue3语法
date: 2022-03-22 11:18:24
toc: true
---

- 每个 Vue 应用都是通过用`createApp`函数创建一个新的应用实例开始的

```js
import { createApp } from "vue";
const app = Vue.createApp({
  /* 选项 */
});
```

- 应用实例暴露的大多数方法都会返回该同一实例，允许链式：

```js
Vue.createApp({})
  .component("SearchInput", SearchInputComponent)
  .directive("focus", FocusDirective)
  .use(LocalePlugin);
```

>[模板语法、组件Props、非Prop的attribute](/All/frame/vue/vite_project/vue3-1 "模板语法")
>[父子组件通信、setup](/All/frame/vue/vite_project/vue3-2 "父子组件通信、setup")

## Vue3认识
>[Vue3认识Ref全家桶](/All/frame/vue/vite_project/ref "Vue3认识Ref全家桶")
>[Vue3认识Reactive全家桶](/All/frame/vue/vite_project/reactive "Vue3认识Reactive全家桶")
>[Vue3认识To全家桶](/All/frame/vue/vite_project/to "Vue3认识To全家桶")
>[Computed、watch、watchEffect](/All/frame/vue/vite_project/cww "Computed、watch、watchEffect")
>[组件和生命周期、父子组件传参、scoped](/All/frame/vue/vite_project/comp "组件和生命周期、父子组件传参、scoped")
>[组件、插槽](/All/frame/vue/vite_project/compAslot "组件、插槽")
>[异步组件、Teleport传送组件、keep-alive缓存组件](/All/frame/vue/vite_project/susTelKeep "异步组件、Teleport传送组件、keep")
>[transiton、transition-group、Bus、TSX](/All/frame/vue/vite_project/tranBusTsx "transiton、transition-group、Bus、TSX")
>[v-model、directive、Hooks](/All/frame/vue/vite_project/hooks "v-model、directive、Hooks")
>[定义全局函数和变量、插件、Scoped样式穿透](/All/frame/vue/vite_project/install "定义全局函数和变量、插件、Scoped样式穿透")
