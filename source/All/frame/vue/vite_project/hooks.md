---
title: v-model、directive、Hooks
date: 2022-03-22 14:43:24
toc: true
---

## 深入v-model
### Vue3自动引入插件
Vue3自动引入插件`unplugin-auto-import/vite`
vite配置
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import VueJsx from '@vitejs/plugin-vue-jsx'
import AutoImport from 'unplugin-auto-import/vite'
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),VueJsx(),AutoImport({
    imports:['vue'],
    dts:"src/auto-import.d.ts"
  })]
})
```
配置完成之后使用ref reactive watch 等 无须import 导入 可以直接使用 

### 默认值的改变
- prop: value -> modelValue;
- 事件: input -> update:modelValue
- v-bind的.sync修饰符和组件的model选项已移除
- 新增支持多个v-model
- 新增支持自定义修饰符
```vue
<!-- 父组件 -->
<ChildCom v-model.modou="flag" v-model:title.taitou="title"></ChildCom>

<!-- 子组件 -->
<script setup lang="ts">
`type` Props = {
  modelValue: boolean,
  title?: string,
  modelModifiers?: {
    modou: boolean
  },
  titleModifiers?: {
    taitou: boolean
  }
}

const propData = defineProps<Props>()
const emit = defineEmits(['update:modelValue', 'update:title']);

const close = () => {
  if (propData?.modelModifiers?.modou) {
    //如果加上了修饰符，那么modou为true
    //...
    emit('update:modelValue', false);
    emit('update:title', '我要改变')
  }
}
</script>
```

## 自定义指令directive
directive-自定义指令（属于破坏性更新）
### 1、Vue3指令的钩子函数
- created: 元素初始化的时候
- beforeMount: 指令绑定到元素后调用，只调用一次
- mounted: 元素插入父级DOM调用
- beforeUpdate: 元素被更新之前调用
- update: 这个周期方法被移除 改用updated
- beforeUnmount: 在元素被移除前调用
- unmounted: 指令被移除后调用 只调用一次
>Vue2 指令 `bind inserted update componentUpdated unbind`

### 2、在setup内定义局部指令
但这里有一个需要注意的限制：必须以 `vNameOfDirective` 的形式来命名本地自定义指令，以使得它们可以直接在模板中使用
```vue
<Dialog v-move-directive="{ background: 'green', flag: show}"></Dialog>

<script setup lang="ts">
const vMoveDirective: Directive = {
  created: () => {
    console.log('初始化');
  },
  beforeMount(...args: Array<any>) {
    console.log('指令绑定到元素后调用，只调用一次==>')
  },
  mounted(el: any, dir: DirectiveBinding<any>) {
    el.style.background = dir.value.background;
    console.log('元素插入父级DOM调用======>')
  },
  beforeUpdate(el: any, dir: DirectiveBinding<any>) {
    console.log('更新之前');
  },
  updated(el: any, dir: DirectiveBinding<any>) {
    console.log('更新之后')
  },
  beforeUnmount(...args: Array<any>) {
    console.log(args);
    console.log('======>卸载之前')
  },
  unmounted(...args: Array<any>) {
    console.log(args);
    console.log('=====>卸载完成')
  }
}
</script>
```

### 3、生命周期钩子参数详解
- 第一个el：当前绑定的DOM元素
- 第二个binding: 
  - instance: 使用指令的组件实例
  - value: 传递给指令的值
  - oldValue: 先前的值，仅在 beforeUpdate 和 updated 中可用。无论值是否有更改都可用。
  - arg: 传递给指令的参数(如果有的话)。例如在 `v-my-directive:foo` 中，arg 为 "foo"
  - modifiers: 包含修饰符(如果有的话) 的对象。例如在 `v-my-directive.foo.bar` 中，修饰符对象为 {foo: true，bar: true}。
  - dir: 一个对象，在组册指令时作为参数传递。
- 第三个：当前元素的虚拟DOM也就是Vnode
- 第四个：prevNode上一个虚拟节点，仅在beforeUpdate和updated钩子中可用

### 4、函数简写
你可能想在 mounted 和 updated 时触发相同行为，而不关心其他的钩子函数。那么你可以通过将这个函数模式实现
```vue
<template>
   <div>
      <input v-model="value" type="text" />
      <A v-move="{ background: value }"></A>
   </div>
</template>
   
<script setup lang='ts'>
import A from './components/A.vue'
import { ref, Directive, DirectiveBinding } from 'vue'
let value = ref<string>('')
type Dir = {
  background: string
}
const vMove: Directive = (el: HTMLElement, binding: DirectiveBinding<Dir>) => {
   el.style.background = binding.value.background
}
</script>
```

## 自定义Hooks
>自定义Hook：主要用来处理一些复用逻辑的一些封装

### Vue2 Mixins
在Vue2中就已经存在了，那就是mixins,mixins就是将这些多个相同的逻辑抽离出来，各个组件只需要引入mixins，就能实现一次写代码，多组件受益的效果。
弊端就是会涉及到覆盖的问题==>**组件的组件的data、methods、filters会覆盖mixins里的同名data、methods、filters**，还有就是变量来源不明确（隐式传入），不利于阅读，是代码变得难以维护。

### Hook
- Vue3 的 hook函数 相当于 vue2 的 mixin, 不同在与 hooks 是函数
- Vue3 的 hook函数 可以帮助我们提高代码的复用性, 让我们能在不同的组件中都利用 hooks 函数
```ts
import { onMounted } from 'vue'
type Options = {
    el: string
}
type Return = {
    Baseurl: string | null
}
export default function (option: Options): Promise<Return> {
  return new Promise((resolve) => {
    onMounted(() => {
      const file: HTMLImageElement = document.querySelector(option.el) as HTMLImageElement;
      file.onload = ():void => {
        resolve({
            Baseurl: toBase64(file)
        })
      }
    })
    const toBase64 = (el: HTMLImageElement): string => {
      const canvas: HTMLCanvasElement = document.createElement('canvas')
      const ctx = canvas.getContext('2d') as CanvasRenderingContext2D
      canvas.width = el.width
      canvas.height = el.height
      ctx.drawImage(el, 0, 0, canvas.width,canvas.height)
      console.log(el.width);
      return canvas.toDataURL('image/png')
    }
  })
}
```
