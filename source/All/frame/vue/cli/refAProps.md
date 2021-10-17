---
title: ref属性/props配置项
toc: true
date: 2021-10-13 17:37:24
---

## ref属性

1. 被用来给元素或子组件注册引用信息（id的替代者）
2. 应用在html标签上获取的是真实DOM元素，应用在组件标签上是组件实例对象（vc）
3. 使用方式：
    1. 打标识：```<h1 ref="xxx">.....</h1>``` 或 ```<School ref="xxx"></School>```
    2. 获取：```this.$refs.xxx```

```html
<template>
  <div id="app">
    <h1 v-text="msg" ref="title"></h1>
    <button ref="btn" @click="showDom">点击我输出上方的dom元素</button>
    <School ref="sch"/>
  </div>
</template>

<script>
...
  methods: {
    showDom() {
      /*
        ref 被用来给元素或子组件注册引用信息。
        引用信息将会注册在父组件的 $refs 对象上。
        如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；
        如果用在子组件上，引用就指向组件实例
      */
      console.log(this.$refs.title);//真实DOM元素
      console.log(this.$refs.btn);//真实DOM元素
      console.log(this.$refs.sch);//School组件实例对象
    },
  },
</script>
```


## props配置项
1. 功能：让组件接收外部传过来的数据
2. 传递数据：```<Demo name="xxx"/>```
3. 接收数据：
    1. 第一种方式（只接收）：```props:['name'] ```
    2. 第二种方式（限制类型）：```props:{name:String}```
    3. 第三种方式（限制类型、限制必要性、指定默认值）：
```js
  props:{
    name:{
      type:String, //类型
      required:true, //必要性
      default:'老王' //默认值
    }
  }
```
> 备注：props是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。

**v-bind动态数据绑定 === js表达式的值,不使用v-bind就是字符串类型**

## scoped样式
>1. 作用：让样式在局部生效
>2. 写法：`<style scoped>`

