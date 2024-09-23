---
title: 组件、插槽
date: 2022-03-22 14:36:24
toc: true
---

## 全局组件、局部组件、递归组件

### 全局组件
>组件使用频率非常高的，几乎每个页面都会用到的便可以封装成全局组件
使用方法：在main.ts引入我们的组件，跟随在createApp产生的Vue实例后面，其次调用component（第一个参数是组件名，第二个是组件实例）
```ts
createApp(App).component('Card', Card).mount('#app')
```
**还可以通过插件的形式，使用.use引入，插件中应该含有install方法**

### 局部组件
在一个组件内部，通过import引入别的组件，称之为局部组件.

### 递归组件
>原理跟我们用js写递归是一样的，自己调用自己，通过一个条件来结束递归，否则会导致内存泄漏。
**案例递归树**
在父组件中配置数据结构，数组对象格式，传给子组件
```ts
type TreeList = {
  name: string,
  icon?: string,
  children?: TreeList[] | []
};
const data = reactive(TreeList[])([
{
    name: "no.1",
    children: [
      {
        name: "no.1-1",
        children: [
          {
            name: "no.1-1-1",
          },
        ],
      },
    ],
  },
  {
    name: "no.2",
    children: [
      {
        name: "no.2-1",
      },
    ],
  },
  {
    name: "no.3",
  },
])
```
子组件接收值
```ts
type TreeList = {
  name: string,
  icon?: string,
  children: TreeList[] | []
};
type Props<T> = {
  data?: T[] | [],
};

defineProps<Props<TreeList>>();//Props<TreeList> => { data?: TreeList[] | [] }
```
**在template中使用**
TreeItem 其实就是当前组件 通过import 把自身又引入了一遍 如果他没有children 了就结束
```vue
<div style="margin-left:10px;" class="tree">
  <div :key="index" v-for="(item,index) in data">
    <div @click='clickItem(item)'>{{item.name}}</div>
    <TreeItem @on-click='clickItem' v-if='item?.children?.length' :data="item.children"></TreeItem>
  </div>
</div>
```

## 动态组件
>什么是动态组件 就是：让多个组件使用同一个挂载点，并动态切换，这就是动态组件
在挂载点使用component标签，然后使用`v-bind:is=”组件”`
用法如下：
引入组件
```js
import A from './A.vue';
import B from './B.vue';
```
```vue
<component :is="A"></component>
```
通过is 切换 A B 组件,使用场景 tab切换 居多

注意事项 

1.在Vue2 的时候is 是通过组件名称切换的 在Vue3 setup 是通过组件实例切换的

2.如果你把组件实例放到Reactive Vue会给你一个警告runtime-core.esm-bundler.js:38 [Vue warn]: Vue received a Component which was made a reactive object. This can lead to unnecessary performance overhead, and should be avoided by marking the component with `markRaw` or using `shallowRef` instead of `ref`. 
Component that was made reactive: 

这是因为reactive 会进行proxy 代理 而我们组件代理之后毫无用处 节省性能开销 推荐我们使用shallowRef 或者  markRaw 跳过proxy 代理

修改如下：
```ts
const tab = reactive<Com[]>([{
  name: 'A组件',
  comName: markRaw(A)
}, {
  name: 'B组件',
  comName: markRaw(B)
}])
```

## 插槽
>插槽就是子组件中的提供给父组件使用使用的一个占位符，用`<slot></slot>`表示，父组件可以在这个占位符中填充任何模板代码，如HTML、组件等，填充的内容会替换子组件中的`<slot></slot>`

### 匿名插槽
```vue
<!-- 子组件上放置 -->
<div>
  <slot></slot>
</div>
<!-- 父组件使用:给插槽填充内容 -->
<Dialog>
  <template v-slot>
    <div>123456</div>
  </template>
</Dialog>
```

### 具名插槽
>具名插槽其实就是给插槽娶个名字。一个组件可以放多个插槽，而且可以放在多个不同的地方，父组件填充内容时，可以根据名字将内容填充到对应的插槽中。
```vue
<!-- 子组件 -->
<div>
  <slot name="header"></slot>
  <slot></slot>
  <slot name="footer"></slot>
</div>
<!-- 父组件 -->
<Dialog>
  <template v-slot:header>
    <div>header</div>
  </template>
  <template v-slot>
    <div>test</div>
  </template>
  <template v-slot:footer>
    <div>footer</div>
  </template>
</Dialog>
```
插槽简写
```vue
<Dialog>
    <template #header>
        <div>header</div>
    </template>
    <template #default>
        <div>test</div>
    </template>
    <template #footer>
        <div>footer</div>
    </template>
</Dialog>
```

### 作用域插槽
在子组件动态绑定参数 派发给父组件的slot去使用
```vue
<div>
  <slot name="header"></slot>
  <div>
    <div v-for="item in 100">
      <slot :data="item"></slot>
    </div>
  </div>
  <slot name="footer"></slot>
</div>
```
通过结构方式取值
```vue
<Dialog>
  <template #header>
    <div>1</div>
  </template>
  <template #default="{ data }">
    <div>{{ data }}</div>
  </template>
  <template #footer>
    <div>3</div>
  </template>
</Dialog>
```

### 动态插槽
插槽可以是一个变量名
```vue
<Dialog>
  <template #[name]>
    <div> 23 </div>
  </template>
</Dialog>

<script>
  const name = ref('header');
</script>
```
