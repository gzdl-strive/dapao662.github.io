---
title: h函数
date: 2022-08-26 10:01:24
toc: true
---

>h函数：采集虚拟DOM节点(vnode)
```ts
// 完整参数签名
function h(
  type: string | Component,
  props?: object | null,
  children?: Children | Slot | Slots
): VNode

// 省略 props
function h(type: string | Component, children?: Children | Slot): VNode

type Children = string | number | boolean | VNode | null | Children[]

type Slot = () => Children

type Slots = { [name: string]: Slot }
```
**详细信息**

- 第一个参数既可以是一个字符串 (用于原生元素) 也可以是一个 Vue 组件定义。
- 第二个参数是要传递的 prop
- 第三个参数是子节点。

当创建一个组件的 vnode 时，子节点必须以插槽函数进行传递。如果组件只有默认槽，可以使用单个插槽函数进行传递。否则，必须以插槽函数的对象形式来传递。

### 使用props传递参数
```js
// 使用props传递参数
const Btn = (props, ctx) => {
  return h(
    'div', 
    {
      class: 'p-2.5 text-white bg-green-500 rounded shadow-lg w-20 text-center'
    },
    props.text
  )
}
```
```html
<Btn text="按钮"></Btn>
```

### 接收emit
```js
const Btn2 = (props, ctx) => {
  return h(
    'div',
    {
      class: 'mt-5 mx-5 cursor-pointer p-2.5 text-white bg-blue-500 rounded shadow-lg w-20 text-center',
      onClick: () => ctx.emit('on-click', 123)
    },
    props.text
  )
}
const getNum = (num) => {
  alert(num);
}
```
```html
<Btn2 text="按钮2" @on-click="getNum"></Btn2>
```

### 定义插槽
```js
const Btn3 = (props, ctx) => {
  console.log(ctx);
  return h(
    'div',
    {
      class: 'mt-5 mx-5 cursor-pointer p-2.5 text-white bg-yellow-500 rounded shadow-lg w-20 text-center',
      onClick: () => ctx.emit('on-click', 999)
    },
    // ctx.slots.default()
    ctx.slots.test({
      name: '按钮Slots'
    })
  )
}
```
```html
<Btn3 text="按钮3" @on-click="getNum">
  <template #default>
    按钮slots
  </template>
  <template #test="{ name }">{{ name }}</template>
</Btn3>
```
