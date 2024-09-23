---
title: 组件和生命周期、父子组件传参、scoped
date: 2022-03-22 14:33:24
toc: true
---

## 组件和生命周期
>组件从创建到销毁的过程称为生命周期
>Vue3的组合式API是没有beforeCreate和created这两个生命周期的，==》setup就相当于这两
- onBeforeMount: 在组件DOM实际渲染之前调用。
- onMounted: 在组件的第一次渲染后调用，该元素现在可用，允许直接DOM访问.
- onBeforeUpdate: 数据更新时调用，发生在虚拟DOM打补丁之前。
- updated: DOM更新后，updated的方法会调用。
- onBeforeUnmounnted: 卸载组件实例之前调用。这个阶段，实例仍然是完全正常的，一般在此阶段，进行清除定时器等操作。
- onUnmounted: 卸载组件实例后调用。

## less和scoped
>Less （Leaner Style Sheets 的缩写） 是一门向后兼容的 CSS 扩展语言。这里呈现的是 Less 的官方文档（中文版），包含了 Less 语言以及利用 JavaScript 开发的用于将 Less 样式转换成 CSS 样式的 Less.js 工具
>Scoped: 实现组件的私有化，当前style属性只属于当前模块,在DOM结构中可以发现，vue通过在DOM结构以及css样式上加了唯一标记，达到样式私有化，不污染全局的作用。
**样式穿透问题：:v-deep >>> /deep/**

## 父子组件传参

### 父传子
>父组件通过在子组件上添加属性传递，非string类型因该使用v-bind绑定，表示一个变量
>子组件接收值,通过defineProps接收，`defineProps`是**无须引入的，直接使用即可**
- typescript可以使用传递字面量类型的纯类型语法作为参数
```ts
//如果使用的是ts
defineProps<{
  title: string,
  data: number[]
}>()
```
- 非ts,即js
```js
defineProps({
  title: {
    type: string,
    default: '',
  },
  data: Array
})
```
- TS特有的默认值方式
**withDefaults是个函数，也是无需引入开箱即用，接收一个Props函数，第二个参数是一个对象设置默认值**
```ts
type Props = {
  title?: string,
  data?: number[],
};
widthDefaults(defineProps<Props>(), {
  title: '张三',
  data: () => [1, 2, 3]
})
```

### 子传父
>可以通过调用父传给子的方法进行传值
>也可以通过defineEmits派发一个事件（composition API里面的）
```ts
//子组件
// setup lang="ts"
const emit = defineEmits(['on-click']);
const clickTap () => {
  emit('on-click', [1, 2, 3]);
}
```
**父组件**
```vue
<template>
  <Menu @on-click="getList"/>
</template>

<script setup lang="ts">
const getList = (list: number[]) => {
  console.log(list, '父组件接收子组件')
}
</script>
```

### 子组件暴露给父组件内部属性
>通过defineExpose
我们从父组件获取子组件实例通过ref
```vue
<Menu ref="meus"/>

<script setup lang="ts">
  const menus = ref(null);
</script>
```
然后打印menus.value 发现没有任何属性
这时候父组件想要读到子组件的属性可以通过 `defineExpose`暴露
```ts
//子组件中通过defineExpose暴露
const list = reactive<number []>([1, 2, 3]);

defineExpose({
  list
})
```
这样父组件就可以读到了
