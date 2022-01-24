---
title: 数据响应式
date: 2022-01-17 09:46:24
toc: true
---

## 什么是数据响应式
>[什么是数据响应式](/All/frame/vue/vue_analysis/reactive/know "什么是数据响应式")

## defineReactive
>[defineReactive函数](/All/frame/vue/vue_analysis/reactive/defineReactive "defineReactive函数")
**通过使用闭包和Object.defineProperty()函数配置，完成数据的劫持/代理**

但是一个对象可能存在多层级的对象，例如:
```js
let obj = {
  a: {
    m: {
      n: 5
    }
  },
  b: 10
}
```
那么我们就需要递归侦测对象的全部属性

## 递归侦测对象的全部属性
>[递归侦测](/All/frame/vue/vue_analysis/reactive/observe "递归侦测")

**现在我们通过在index.js中调用observe函数，进入observe，判断当前传入的数据是否是一个对象，如果不是就什么也不做，如果是对象，那么通过判断当前对象上是否存在__ob__属性，如果不存在，说明还没有被Observer类处理过，那么需要使用Observer类进行初始化**

**Observer类给对象添加__ob__属性，并且遍历该对象，通过defineReactive进行数据劫持,而在defineReactive里通过调用observe函数，来对自元素进行递归**

>这样就形成了三个函数递归调用的关系，给对象的每个子元素都进行数据劫持

## 数组的响应式处理
>[数组的响应式处理](/All/frame/vue/vue_analysis/reactive/array "数组的响应式处理")

**对数组进行特殊处理，对数组方法进行重写，并把原型指向重写后的新对象上，重写的新对象不能丢弃原来数组方法的功能，如果是新增加的项，我们也需要把他们变成响应式的，所以需要通过__ob__调用Observer类上的处理数组的方法**

## 依赖收集
- 需要用到数据的地方，称为依赖
- Vue1.x，细粒度依赖，用到数据的DOM都是依赖； 
- Vue2.x，中等粒度依赖，用到数据的组件是依赖； 
- 在getter中收集依赖，在setter中触发依赖

>[依赖收集Dep类和Watcher类](/All/frame/vue/vue_analysis/reactive/dependenice)
