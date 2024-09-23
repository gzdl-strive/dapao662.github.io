---
title: Computed、watch、watchEffect
date: 2022-03-22 14:30:24
toc: true
---

## Computed用法
>计算属性就是当依赖的属性的值发生变化的时候，才会触发它的更改，如果依赖的值不发生变化时，使用的是缓存中的属性值。

- 函数形式
```js
import { computed, reactive, ref } from 'vue'
let price = ref(0)//$0
let m = computed<string>(()=>{
  return `$` + price.value
})
price.value = 500
```

- 对象形式
```vue
<template>
   <div>{{ mul }}</div>
   <div @click="mul = 100">click</div>
</template>
 
<script setup lang="ts">
import { computed, ref } from 'vue'
let price = ref<number | string>(1)//$0
let mul = computed({
   get: () => {
      return price.value
   },
   set: (value) => {
      price.value = 'set' + value
   }
})
</script>
```

## 认识watch侦听器
watch 需要侦听特定的数据源，并在单独的回调函数中执行副作用
- watch第一个参数: 监听源
- watch第二个参数: 回调函数cb（newVal,oldVal）
- watch第三个参数一个options配置项是一个对象

### 侦听单一源
>侦听器数据源可以是一个具有返回值的 getter 函数，也可以直接是一个 ref
```js
// 侦听一个 getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)

// 直接侦听一个 ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```

### 侦听多个源
>侦听器还可以使用数组以同时侦听多个源
```js
watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* ... */
})
```

## 认识watchEffect高级侦听器
>立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数
>如果用到message 就只会监听message 就是用到几个监听几个 而且是非惰性 会默认调用一次

### 清除副作用
>就是在触发监听之前会调用一个函数可以处理你的逻辑例如防抖
```js
import { watchEffect, ref } from 'vue'
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect((oninvalidate) => {
    oninvalidate(()=>{})
    console.log('message2', message2.value);
})
```

### 停止跟踪 watchEffect 返回一个函数 调用之后将停止更新
```js
const stop =  watchEffect((oninvalidate) => {
    oninvalidate(()=>{})
    console.log('message2', message2.value);
})
stop()
```

### 更多配置项
>副作用刷新时机`flush`，一般使用post
- pre: 组件更新前执行
- sync: 强制效果始终同步触发
- post: 组件更新后执行
**如果在副作用中获取dom节点，但是dom节点还没挂载或更新，此时将flush设置为post即可.**

### 侦听器调试(onTrack/onTrigger)
**onTrack将在响应式property或ref作为依赖项被追踪时被调用**
**`onTrigger`可以帮助我们调试 watchEffect， onTrigger将在依赖项变更导致副作用被付出时被调用**
```js
import { watchEffect, ref } from 'vue'
let message = ref<string>('')
let message2 = ref<string>('')
 watchEffect((oninvalidate) => {
    oninvalidate(()=>{ })
    console.log('message2', message2.value);
},{
    flush:"post",
    onTrigger () {
       debugger 
    }
})
```
