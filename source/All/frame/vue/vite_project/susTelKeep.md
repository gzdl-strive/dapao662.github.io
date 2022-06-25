---
title: 异步组件、Teleport传送组件、keep
date: 2022-03-22 14:38:24
toc: true
---

## 异步组件&代码分包&suspense
>setup语法糖中可以使用顶层`await`，结果代打会被编译成`async setup()`

### 异步组件
在大型应用中，我们可能需要将应用分割成小一些的代码块 并且减少主包的体积 -> 这时候就可以使用异步组件

**父组件引用子组件 通过`defineAsyncComponent`加载异步配合`import 函数模式`便可以分包**
```vue
<script setup lang="ts">
import { reactive, ref, markRaw, toRaw, defineAsyncComponent } from 'vue'
 
const Dialog = defineAsyncComponent(() => import('../../components/Dialog/index.vue'))
</script>
```

### Suspense
>`<suspense>` 组件有两个插槽。它们都只接收一个直接子节点。default 插槽里的节点会尽可能展示出来。如果不能，则展示 fallback 插槽里的节点
```vue
<Suspense>
  <template #default>
      <Dialog>
          <template #default>
              <div>我在哪儿</div>
          </template>
      </Dialog>
  </template>

  <template #fallback>
      <div>loading...</div>
  </template>
</Suspense>
```

## Teleport传送组件
>Teleport是一种能够将我们的模板渲染值指定DOM节点，不受父级style、v-show等属性影响，但data、props数据依旧能够共用的技术；类似于React的Portal;
**主要解决的问题，因为Teleport节点挂载在其他指定的DOM节点下，完全不受父级style样式影响**

使用方法
>通过to属性 插入指定元素位置`to="body"`便可以将Teleport内容传送至指定位置
```vue
<Teleport to="body">
  <Loading> </Loading>
</Teleport>
```
也可以自定义传送位置 支持 class id等 选择器
也可以使用多个
```vue
<Teleport to=".modal1">
     <Loading></Loading>
</Teleport>
<Teleport to=".modal2">
     <Loading></Loading>
</Teleport>
```

## keep-alive缓存组件
内置组件keep-alive
有时候我们不希望组件被重新渲染影响使用体验；或者处于性能考虑，避免多次重复渲染降低性能。而是希望组件可以缓存下来,维持当前的状态。这时候就需要用到`keep-alive`组件。
开启keep-alive生命周期的变化
>- 初次进入时：onMounted > onActivated
>- 退出后触发：deactivated
>- 再次进入：只会触发onActivated
>- 事件挂载的方法等，只执行一次的放置onMounted中；组件每次进去执行的方法放在onActivated中
```vue
<!-- 基本 -->
<keep-alive>
  <component :is="view"></component>
</keep-alive>
 
<!-- 多个条件判断的子组件 -->
<keep-alive>
  <comp-a v-if="a > 1"></comp-a>
  <comp-b v-else></comp-b>
</keep-alive>
 
<!-- 和 `<transition>` 一起使用 -->
<transition>
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>
</transition>
```
**include和exclude**
```vue
 <keep-alive :include="" :exclude="" :max=""></keep-alive>
```
`include` 和 `exclude prop` 允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示。
**max**
```vue
<keep-alive :max="10">
  <component :is="view"></component>
</keep-alive>
```
