---
title: 响应式数据的判断
date: 2021-10-17 18:30:24
toc: true
---

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理

```html
<template>
  <h3>我是App组件</h3>
</template>

<script>
  import {
    ref,
    reactive,
    toRefs,
    readonly,
    isRef,
    isReactive,
    isReadonly,
    isProxy,
  } from "vue";
  export default {
    name: "App",
    setup() {
      let car = reactive({ name: "奔驰", price: "40W" });
      let sum = ref(0);
      let car2 = readonly(car);

      console.log(isRef(sum));
      console.log(isReactive(car));
      console.log(isReadonly(car2));
      console.log(isProxy(car));
      console.log(isProxy(sum));

      return { ...toRefs(car) };
    },
  };
</script>

<style>
  .app {
    background-color: gray;
    padding: 10px;
  }
</style>
```
