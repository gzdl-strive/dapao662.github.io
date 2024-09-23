---
title: 定义全局函数和变量、插件、Scoped样式穿透
date: 2022-03-22 14:45:24
toc: true
---

## Vue3定义全局函数和变量
由于Vue3没有Prototype属性 使用app.config.globalProperties代替，然后去定义变量和函数
```ts
// Vue2
Vue.prototype.$http = () => {}

// Vue3
const app = createApp({});
app.config.globalProperties.$http = () => {}
```

### 过滤器
>Vue3移除了过滤器，我们可以自定义一个全局函数代替
```ts
app.config.globalProperties.$filters = {
  format<T extends any>(str: T): string {
    return `$${str}`
  }
}
```
声明文件 不然TS无法正确类型 推导
```ts
type Filter = {
    format: <T extends any>(str: T) => T
  }
// 声明要扩充@vue/runtime-core包的声明.
// 这里扩充"ComponentCustomProperties"接口, 因为他是vue3中实例的属性的类型.
  declare module '@vue/runtime-core' {
    export interface ComponentCustomProperties {
        $filters: Filter
    }
  }
```
我们可以直接在模板中使用$filter,但是如果想要在setup中读取值
**setup读取**
```ts
import { getCurrentInstance, ComponentInternalInstance } from 'vue';
 
const { appContext } = <ComponentInternalInstance>getCurrentInstance()
 
console.log(appContext.config.globalProperties.$env);
```

## 编写Vue3插件
>插件是自包含的代码，通常向 Vue 添加全局级功能。你如果是一个对象需要有install方法Vue会帮你自动注入到install 方法 你如果是function 就直接当install 方法去使用

使用插件
在使用 createApp() 初始化 Vue 应用程序后，你可以通过调用 use() 方法将插件添加到你的应用程序中。

## 了解ElementUI, AntDesign
>看官网

## 详解Scoped和样式穿透
>主要用于修改很多vue常用的组件库(element,vant,AntDisign)，虽然配好了样式，但是还是需要更改其他样式

**就需要用到了样式穿透**

### scoped样式隔离的原理
vue中的scoped 通过在DOM结构以及css样式上加唯一不重复的标记:`data-v-hash`的方式，以保证唯一（而这个工作是由过PostCSS转译实现的），达到样式私有化模块化的目的。

总结一下scoped三条渲染规则：
  - 给HTML的DOM节点加一个不重复data属性(形如：data-v-123)来表示他的唯一性
  - 在每句css选择器的末尾（编译后的生成的css语句）加一个当前组件的data属性选择器（如[data-v-123]）来私有化样式
  - 如果组件内部包含有其他组件，只会给其他组件的最外层标签加上当前组件的data属性

PostCSS会给一个组件中的所有dom添加了一个独一无二的动态属性data-v-xxxx，然后，给CSS选择器额外添加一个对应的属性选择器来选择该组件中dom，这种做法使得样式只作用于含有该属性的dom——组件内部dom, 从而达到了'样式模块化'的效果

### 案例
```vue
<el-input class="ipt" />

<style scoped lang="less">
.ipt {
  input {
    background: red;
  }
}
</style>
```
发现没有生效
>如果不写Scoped 就没问题, 原因就是Scoped 搞的鬼 他在进行PostCss转化的时候把元素选择器默认放在了最后

**Vue 提供了样式穿透`:deep()` 他的作用就是用来改变 属性选择器的位置**
```vue
<style scoped lang="less">
.ipt {
  :deep(input) {
    background: red;
  }
}
</style>
```