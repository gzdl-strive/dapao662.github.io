---
title: 对比ref和reactive
toc: true
date: 2021-10-17 14:34:24
---

## 对比ref和reactive
-  从定义数据角度对比：
   -  ref用来定义：<strong style="color:#DD5145">基本类型数据</strong>。
   -  reactive用来定义：<strong style="color:#DD5145">对象（或数组）类型数据</strong>。
   -  备注：ref也可以用来定义<strong style="color:#DD5145">对象（或数组）类型数据</strong>, 它内部会自动通过```reactive```转为<strong style="color:#DD5145">代理对象</strong>。
-  从原理角度对比：
   -  ref通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）。
   -  reactive通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。
-  从使用角度对比：
   -  ref定义的数据：操作数据<strong style="color:#DD5145">需要</strong>```.value```，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>```.value```。
   -  reactive定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>```.value```。

## setup的两个注意点
- setup执行的时机
  - 在beforeCreate之前执行一次，this是undefined。
  
- setup的参数
  - props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性。
  - context：上下文对象
    - attrs: 值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于 ```this.$attrs```。
    - slots: 收到的插槽内容, 相当于 ```this.$slots```。
    - emit: 分发自定义事件的函数, 相当于 ```this.$emit```。

```html
<!-- App.vue -->
<Demo @hello="showHelloMsg" msg="你好啊" school="尚硅谷">
  <template v-slot:qwe>
    <span>尚硅谷</span>
  </template>
  <template v-slot:asd>
    <span>尚硅谷</span>
  </template>
</Demo>
```

```html
<!-- Demo.vue -->
<script>
export default {
  name: 'Demo',
  props: ['msg', 'school'],
  emits: ['hello'].
  setup(props, context) {
    console.log('---setup---',props)
    console.log('---setup---',context)
    console.log('---setup---',context.attrs) //相当与Vue2中的$attrs
    console.log('---setup---',context.emit) //触发自定义事件的。
    console.log('---setup---',context.slots) //插槽
  }
}
</script>
```
