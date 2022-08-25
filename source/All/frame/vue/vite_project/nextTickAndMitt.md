---
title: EventLoop-nextTick、Mitt
date: 2022-08-25 09:40:24
toc: true
---

## EventLoop,nextTick
### 为什么JS不能是多线程的?
>同一时间同时操作同一个DOM，一个增加、一个删除，JS就不知道要干啥了，所以JS是单线程的，即使是后来支持了多线程webWorker，但是也是不允许操作DOM的

### 同步任务
>代码从上到下顺序执行

### 异步任务
JS是单线程的：所有的任务需要排队，后面的任务需要等待前面的任务执行完才行
>如果前面的任务耗时过长，后面的任务就需要一直等待，一些从用户角度上不需要等待的任务就会一直等待，这个从体验角度上来讲是不可接受的，所以JS中就出现了异步的概念 

* （1）宏任务(macro task)：script整体代码、setTimeout、setInterval、UI交互事件、postMessage、Ajax
* （2）微任务(micro task)：Promise.then，catch,finally、mutaionObserver、process.nextTick(Node.js环境)

>运行机制：所有同步任务都是在主线程执行时形成一个执行栈,主线程外还存在一个“事件队列” ===> js引擎遇到一个异步事件会将这个事件挂起，继续执行执行栈中的其他任务。当异步事件返回结果后，js会将这个事件加入到事件队列中。被放入事件队列不会立即执行其回调，而是等待当前执行栈中的所有任务都执行完毕，主线程处于闲置时，主线程会去事件队列中查找是否有任务，如果有，那么主线程会从中取出排在第一位的事件，并把这个事件对应的回调放入执行栈中，然后执行其中的同步代码...,如此反复。

### nextTick
>nextTick就是创建一个异步任务

**作用**：等待下一次DOM更新刷新的工具方法
`export declare function nextTick<T = void>(this: T, fn?: (this: T) => void): Promise<void>;`

**详细信息**：当你在Vue中更改响应式状态时，最终的DOM更新并不是同步生效的，而是由Vue将它们缓存在一个队列中，直到下一个"Tick"才一起执行。这样是为了确保每个组件无论发生多少状态改变，都仅执行一次更新。

nextTick()可以在状态改变后立即使用，以等待DOM更新完成。你可以传递一个回调函数作为参数，或者await返回的promise

## Mitt事件总线
>A组件，用于派发Emit
```html
<script setup>
import { getCurrentInstance } from 'vue';

const instance = getCurrentInstance();
const handleEmit1 = () => {
  instance?.proxy?.$Bus.emit('on-num', 100);
}
const handleEmit2 = () => {
  instance?.proxy?.$Bus.emit('*****', 500);
}
</script>

<template>
  <div>
    <h1>Mitt-A组件->派发Emit</h1>
    <button @click="handleEmit1">emit1</button>
    <button @click="handleEmit2">emit2</button>
  </div>
</template>
```

>B组件，用于监听on
```html
<script setup>
import { getCurrentInstance } from 'vue';
const instance = getCurrentInstance();

// 监听单个事件
instance?.proxy?.$Bus.on('on-num', (num) => {
  console.log(num, '====>Mitt2');
});
// 监听所有事件
instance?.proxy?.$Bus.on('*', (type, num) => {
  console.log(type, num, '====>Mitt2222')
})

// 移除监听事件off
const Fn = num => {
    console.log(num, '===========>B')
}
instance?.proxy?.$Bus.on('on-num',Fn)//listen
instance?.proxy?.$Bus.off('on-num',Fn)//unListen

// 清空所有监听
instance?.proxy?.$Bus.all.clear() 
</script>
```