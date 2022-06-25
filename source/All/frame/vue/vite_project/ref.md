---
title: Vue3 认识 Ref 全家桶
date: 2022-03-22 11:29:24
toc: true
---

### ref

> 接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象仅有一个 `.value` property，指向该内部值。

Ref Ts 对应的接口

```ts
interface Ref<T> {
  value: T;
}
```

定义 ref 变量的方法
**被 ref 包装后需要.value 进行赋值**

```vue
<script setup lang="ts">
import { ref, Ref } from "vue";
let message = ref<string>("message");
let message2: Ref<string> = ref("message2");
</script>
```

### isRef

> 判断是不是一个 ref 对象

### shallowRef

> 创建一个跟踪自身.value 变化的 ref,但不会使其值也变成响应式的

修改器属性是非响应式的，这样是不会改变的
```vue
<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>

<script setup lang="ts">
import { Ref, shallowRef } from "vue";
type Obj = {
  name: string;
};
let message: Ref<Obj> = shallowRef({
  name: "ces",
});

const changeMsg = () => {
  //修改器属性是非响应式的，这样是不会改变的
  message.value.name = "测试666";
  //这样是可以被监听到的修改value
  message.value = {
    name: '测试666'
  }
};
</script>
```

### triggerRef
强制更新页面DOM
```vue
<template>
  <div>
    <button @click="changeMsg">change</button>
    <div>{{ message }}</div>
  </div>
</template>
<script setup lang="ts">
import { Ref, shallowRef,triggerRef } from 'vue'
type Obj = {
  name: string
}
let message: Ref<Obj> = shallowRef({
  name: "测试"
})
 
const changeMsg = () => {
  message.value.name = '测试666'
  //这样也是可以改变值的
  triggerRef(message);
}
</script> 
```

### customRef
>自定义ref
>customRef是个工厂函数，要求我们返回一个对象并且实现get和set
```vue
<script setup lang="ts">
import { customRef } from 'vue';

function Myref<T>(value: T) {
  return customRef((track, trigger) => {
    return {
      get() {
        track()
        return value
      },
      set(newVal: T) {
        console.log('set');
        value = newVal
        trigger()
      }
    }
  })
}
</script>
```
