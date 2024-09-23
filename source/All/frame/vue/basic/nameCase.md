---
title: 姓名案例（插值/methods/computed/computed简写）
date: 2021-10-13 15:58:24
toc: true
---

### 1、为什么要有计算属性
初衷是什么?

>模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护

在这个模板里面就不是简单的声明式逻辑了 必须要看一段时间才能看懂 所以对于任何复杂的逻辑你都应该使用计算属性

### 2、计算属性 vs 方法
- 可以通过在表达式中调用方法来达到同样的效果

- 两种方式的最终结果确实是完全相同的。然而，不同的是计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖发生改变时它们才会重写求值.

例子:
```html
<div id="app">
  姓: <input type="text" v-model="firstName"> <br />
  名: <input type="text" v-model="lastName"> <br />
</div>
<script>
  const vm = new Vue({
    data() {
      return {
        firstName: '张',
        lastName: '三'
      }
    }
  }).$mount('#app');
<script>
```

### 插值语法
**直接拼接**
```html
<span>{{firstName}}-{{lastName}}</span>
```

### methods方法
```html
<span>{{ fullName() }}</span>

<script>
  new Vue({
    ...
    methods: {
      fullName() {
        return this.firstName + '-' + this.lastName;
      }
    }
  })
</script>
```

### computed
1. 定义：要用的属性不存在，要通过已有属性计算得来。
2. 原理：底层借助了Object.defineProperty方法提供的getter和setter
3. get函数什么时候执行？
  - (1).初次读取时会执行一次
  - (2).当依赖的数据发生变化时会被再次调用
4. 优势：与methods实现相比，内部有缓存机制(复用)，效率更高，调试方便
5. 备注：
  - (1).计算属性最终会出现在vm上，直接读取使用即可
  - (2).如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生变化
```html
<span>{{ fullName }}</span>
<script>
  new Vue({
    computed: {
      fullName: {
        //get有什么作用？
        //当有人读取fullName时，且返回值就作为fullName的值
        //get什么时候调用？ 
        //1.初次读取fullName时 2.所依赖的数据发生变化时
        get() {
          console.log('get被调用了')
          return this.firstName + '-' + this.lastName;
        },
        //set什么时候调用，当fullName被修改时
        set(newVal) {
          console.log('set被调用了', newVal)
          const arr = newVal.split('-');
          this.firstName = arr[0];
          this.lastName = arr[1];
        }
      }
    }
  })
</script>
```

### computed简写
```html
<script>
new Vue({
  computed: {
    //一旦我们确定计算属性只读取不修改才可以使用简写
    //简写
    fullName() {
      return this.firstName + '-' + this.lastName;
    }
  }
})
</script>
```
