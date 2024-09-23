---
title: transiton、transition-group、Bus、TSX
date: 2022-03-22 14:41:24
toc: true
---

## transiton动画组件
Vue提供了transition的封装组件，可以给任何元素和组件添加进入/离开过度：
- 条件渲染（使用v-if）
- 条件展示(使用v-show)
- 动态组件
- 组件根节点
自定义 transition 过度效果，你需要对**transition组件**的**name属性**自定义。并在css中写入对应的样式

### 1、过渡的类名
在进入/离开的过渡中，会有 6 个 class 切换。
> v-enter-from、v-enter-active、v-enter-to、v-leave-from、v-leave-active、v-leave-to
```
<button @click="flag = !flag">切换</button>
<transition name='fade'><div v-if="flag"></div></transition>

<style>
/* 开始过度 */
.fade-enter-from{
   background:red;
   width:0px;
   height:0px;
   transform:rotate(360deg)
}
/* 开始过度了 */
.fade-enter-active{
  transition: all 2.5s linear;
}
/* 过度完成 */
.fade-enter-to{
   background:yellow;
   width:200px;
   height:200px;
}
/* 离开的过度 */
.fade-leave-from{
  width:200px;
  height:200px;
  transform:rotate(360deg)
}
/* 离开中过度 */
.fade-leave-active{
  transition: all 1s linear;
}
/* 离开完成 */
.fade-leave-to{
  width:0px;
   height:0px;
}
</style>
```

### 2、自定义过渡class类名
trasnsition props
- enter-from-class
- enter-active-class
- enter-to-class
- leave-from-class
- leave-active-class
- leave-to-class
**自定义过度时间 单位毫秒, 你也可以分别指定进入和离开的持续时间**
```vue
<transition :duration="1000">...</transition>
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```
通过自定义class结合css动画库animate.css
```vue
<transition
  leave-active-class="animate__animated animate__bounceInLeft"
  enter-active-class="animate__animated animate__bounceInRight"
>
  <div v-if="flag" class="box"></div>
</transition>
```

### 3、transition生命周期 8 个
```js
@before-enter="beforeEnter" //对应enter-from
@enter="enter"//对应enter-active
@after-enter="afterEnter"//对应enter-to
@enter-cancelled="enterCancelled"//显示过度打断
@before-leave="beforeLeave"//对应leave-from
@leave="leave"//对应enter-active
@after-leave="afterLeave"//对应leave-to
@leave-cancelled="leaveCancelled"//离开过度打断
```
当只用 JavaScript 过渡的时候，在 enter 和 leave 钩子中必须使用 done 进行回调
```ts
const beforeEnter = (el: Element) => {
    console.log('进入之前from', el);
}
const Enter = (el: Element,done:Function) => {
    console.log('过度曲线');
    setTimeout(()=>{
       done()
    },3000)
}
const AfterEnter = (el: Element) => {
    console.log('to');
}
```

### 4、appear
通过这个属性可以设置初始节点过度 就是页面加载完成就开始动画 对应三个状态
```ts
appear-active-class=""
appear-from-class=""
appear-to-class=""
appear
```

## transition-group过度列表
- 单个节点
- 多个节点，每次只渲染一个
那么怎么同时渲染整个列表，比如使用 v-for？在这种场景下，我们会使用 `<transition-group>` 组件。在我们深入例子之前，先了解关于这个组件的几个特点：
- 默认情况下，他不会渲染一个包裹元素，但是你可以通过tag attribute指定渲染一个元素。
- 过渡模式不可用，因为我们不再相互切换特有的元素
- 内部元素**总是需要**提供唯一的key attritube值
- CSS过渡的类会将应用在内部的元素中，而不是这个组/容器本身。
```js
<transition-group>
  <div style="margin: 10px;" :key="item" v-for="item in list">{{ item }</div>
</transition-group>

<script lang="ts">
const list = reactive<number[]>([1, 2, 4, 5, 6, 7, 8, 9])
const Push = () => {
    list.push(123)
}
const Pop = () => {
    list.pop()
}
</script>
```

### 列表的移动过渡
<transition-group> 组件还有一个特殊之处。除了进入和离开，它还可以为定位的改变添加动画。只需了解新增的 `v-move` 类就可以使用这个新功能，它会应用在元素改变定位的过程中。像之前的类名一样，它的前缀可以通过 name attribute 来自定义，也可以通过 move-class attribute 手动设置
```vue
<template>
    <div>
        <button @click="shuffle">Shuffle</button>
        <transition-group class="wraps" name="mmm" tag="ul">
            <li class="cell" v-for="item in items" :key="item.id">{{ item.number }}</li>
        </transition-group>
    </div>
</template>
  
<script setup  lang='ts'>
import _ from 'lodash'
import { ref } from 'vue'
let items = ref(Array.apply(null, { length: 81 } as number[]).map((_, index) => {
    return {
        id: index,
        number: (index % 9) + 1
    }
}))
const shuffle = () => {
    items.value = _.shuffle(items.value)
}
</script>
  
<style scoped lang="less">
.wraps {
    display: flex;
    flex-wrap: wrap;
    width: calc(25px * 10 + 9px);
    .cell {
        width: 25px;
        height: 25px;
        border: 1px solid #ccc;
        list-style-type: none;
        display: flex;
        justify-content: center;
        align-items: center;
    }
}
 
.mmm-move {
    transition: transform 0.8s ease;
}
</style>
```
### 状态过渡
Vue 也同样可以给数字 Svg 背景颜色等添加过度动画 今天演示数字变化
```vue
<template>
    <div>
        <input step="20" v-model="num.current" type="number" />
        <div>{{ num.tweenedNumber.toFixed(0) }}</div>
    </div>
</template>
    
<script setup lang='ts'>
import { reactive, watch } from 'vue'
import gsap from 'gsap'
const num = reactive({
    tweenedNumber: 0,
    current:0
})
 
watch(()=>num.current, (newVal) => {
    gsap.to(num, {
        duration: 1,
        tweenedNumber: newVal
    })
})
 
</script>
    
<style>
</style>
```

## 依赖注入Provide/inject
通常，当我们需要从父组件向子组件传递数据时，我们使用 props。想象一下这样的结构：有一些深度嵌套的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。
provide 可以在祖先组件中指定我们想要提供给后代组件的数据或方法，而在任何后代组件中，我们都可以使用 inject 来接收 provide 提供的数据或方法。 
**父组件传递数据**
```vue
<template>
    <div class="App">
        <button>我是App</button>
        <A></A>
    </div>
</template>
    
<script setup lang='ts'>
import { provide, ref } from 'vue'
import A from './components/A.vue'
let flag = ref<number>(1)
provide('flag', flag)
</script>
    
<style>
.App {
    background: blue;
    color: #fff;
}
</style>
```
**子组件接收**
```vue
<template>
    <div style="background-color: green;">
        我是B
        <button @click="change">change falg</button>
        <div>{{ flag }}</div>
    </div>
</template>
    
<script setup lang='ts'>
import { inject, Ref, ref } from 'vue'
 
const flag = inject<Ref<number>>('flag', ref(1))
 
const change = () => {
    flag.value = 2
}
</script>
    
<style>
</style>
```
**TIPS 你如果传递普通的值 是不具有响应式的 需要通过ref reactive 添加响应式**
使用场景: 当父组件有很多数据需要分发给其子代组件的时候， 就可以使用provide和inject

## 兄弟组件传参和Bus

### 1、借用父组件传参
父组件App,子组件A,B
A 组件派发事件通过App.vue, App.vue接受A组件派发的事件然后在Props 传给B组件 也是可以实现的

**缺点就是比较麻烦 ，无法直接通信，只能充当桥梁**
我们在Vue2 可以使用$emit 传递 $on监听 emit传递过来的事件
这个原理其实是运用了JS设计模式之发布订阅模式

### 2、Event Bus事件总线
>发布订阅模式
```ts
// Vue3发布订阅模式--用于兄弟组件通信
type BusClass<T> = {
  emit: (name: T) => void,
  on: (name: T, callback: Function) => void
}

type BusParams = string | number | symbol

type List = {
  [key: BusParams]: Array<Function>
}

class Bus<T extends BusParams> implements BusClass<T>{
  list: List
  constructor() {
    this.list = {};
  }
  emit(name: T, ...args: Array<any>) {
    let eventName: Array<Function> = this.list[name];
    eventName.forEach(v => {
      v.apply(this, args);
    })
  }
  on(name: T, callback: Function) {
    let fn: Array<Function> = this.list[name] || [];
    fn.push(callback);
    this.list[name] = fn;
  }
}

export default new Bus();
```

## TSX
我们之前使用Template去写模板，现在还可以扩展另一种风格--》TSX风格

**安装插件: `npm install @vitejs/plugin-vue-jsx -D`**
vite.config.ts配置
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueJsx from '@vitejs/plugin-vue-jsx';
// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(),vueJsx()]
})
```
**修改tsconfig.json配置文件**
```json
{
  "compilerOptions": {
    //...
    "jsx": "preserve",
    "jsxFactory": "h",
    "jsxFragmentFactory": "Fragment",
  }
}
```
>tsx不会自动解包，使用ref需要加上.value

### tsx
>支持v-model,v-show
```tsx
import { ref } from 'vue'
let v = ref<string>('');
let flag = ref<boolean>(true)
const RenderDom = () => {
  return (
    <>
      <input v-show={flag.value} type="text" v-model={v.value}></input>
    </>
  )
}
export default RenderDom;
```
>不支持v-for(使用map)、v-if(可以使用三元表达式)
>v-bind：直接赋值`data-arr={arr}`
>绑定事件，安装react风格,事件以on开头，事件名称首字母大写: onClick...
>Props接收值: 函数第一个参数是Props,第二个参数是context上下文->(包含attrs、emit、slots三个参数)
>Emit派发，调用context的emit参数即可
