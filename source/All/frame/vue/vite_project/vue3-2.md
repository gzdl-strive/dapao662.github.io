---
title: 父子组件通信、setup
date: 2022-03-22 11:25:24
toc: true
---

## 父子组件通信

```js
//父组件
<child-comp v-model="keywords"></child-comp>

//...
  setup() {
    const keywords = ref<string>('');

    return {
      keywords
    }
  }
//...
```

在子组件上接收:

```ts
//子组件
props: {
  modelValue: {
    type: String as PropType<string>,
    required: true
  }
},
emits: ['update:modelValue']
```

## setup 的第一个参数 props 不能解构

因为 props 是响应式的，你不能使用 ES6 解构，它会消除 prop 的响应性。
如果一定需要解构，可以在`setup`函数中使用`toRefs`函数来完成

```js
import { toRefs } from 'vue';

setup(props) {
  const { title } = toRefs(props);
}
```

## setup 的第二个参数 context 是一个普通的 js 对象，即不是响应式的，可以安全的使用解构

```js
export default {
  setup(props, context) {
    // setup(props,  { attrs, slots, emit, expose }) {
    // Attribute (非响应式对象，等同于 $attrs)
    console.log(context.attrs);

    // 插槽 (非响应式对象，等同于 $slots)
    console.log(context.slots);

    // 触发事件 (方法，等同于 $emit)
    console.log(context.emit);

    // 暴露公共 property (函数)
    console.log(context.expose);
  },
};
```
