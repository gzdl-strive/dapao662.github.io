---
title: shallowReactive
date: 2021-10-17 18:16:24
toc: true
---

- shallowReactive：只处理对象最外层属性的响应式（浅响应式）。
- shallowRef：只处理基本数据类型的响应式, 不进行对象的响应式处理。

- 什么时候使用?
  - 如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> shallowReactive。
  - 如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换 ===> shallowRef。

```js
import { toRefs, shallowReactive, shallowRef } from "vue";
export default {
  name: "Demo",
  setup() {
    //数据
    let person = shallowReactive({ //只考虑第一层数据的响应式
    // let person = reactive({
      name: "张三",
      age: 18,
      job: {
        j1: {
          salary: 20,
        },
      },
    });
    let x = shallowRef({
      y: 0,
    });
    console.log("******", x);

    //返回一个对象（常用）
    return {
      x,
      person,
      ...toRefs(person),
    };
  },
};
```
