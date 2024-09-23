---
title: 绑定样式/条件渲染/列表渲染
date: 2021-10-13 16:32:24
toc: true
---

### 绑定样式：

1. class 样式：
   写法 `:class="xxx"` xxx 可以是字符串、数组、对象

- 字符串写法适用于：类名不确定，要动态获取
- 数组写法适用于：要绑定多个样式，个数不确定，名字也不确定
- 对象写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用

2. style 样式
   `:style="{fontSize: xxx}"` 其中 xxx 是动态值
   :style="[a, b]"其中 a,b 是样式对象

```html
<div id="app">
  <!-- 绑定class样式--字符串写法,适用于：样式的类名不确定，需要动态指定 -->
  <div class="basic" :class="mood" @click="changeMood">{{ name }}</div>
  <br /><br />
  <!-- 绑定class样式--数组写法，适用于：要绑定的样式个数不确定，名字也不确定 -->
  <div class="basic" :class="classArr">{{ name }}</div>
  <br /><br />
  <!-- 绑定class样式--对象写法，适用于：要绑定的样式个数确定，名字也确定，但要动态决定用不用 -->
  <div class="basic" :class="classObj">{{ name }}</div>
  <br /><br />

  <!-- 绑定style样式--对象写法 -->
  <div class="basic" :style="styleObj">{{ name }}</div>
  <br /><br />
  <!-- 绑定style样式--数组写法 -->
  <div class="basic" :style="styleArr">{{ name }}</div>
</div>
<script>
  new Vue({
    data() {
      return {
        mood: "normal",
        classArr: ["demo1", "demo2", "demo3"],
        classObj: {
          demo1: false,
          demo2: false,
        },
        styleObj: {
          fontSize: "40px",
        },
        styleArr: [
          {
            fontSize: "40px",
          },
          {
            color: "orange",
          },
        ],
      };
    },
  });
</script>
```

### 条件渲染
1. v-if
写法：
  (1). v-if="表达式"
  (2). v-else-if="表达式"
  (3). v-else="表达式"
  **适用于：切换频率比较低的场景**
  **特点：不展示的DOM元素直接被移除**
  注意：v-if可以和v-else-if,v-else一起使用，但要求结构不能被"打断""
2. v-show
写法: v-show="表达式"
**适用于：切换频率较高的场景**
**特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉`(display: none)`**
3. 备注：使用v-if时，元素可能无法获取到，而使用v-show一定可以获取到

### 列表渲染
v-for指令：
1. 用于展示列表数据
2. 语法：v-for="(item, index) in xxx" :key="yyy"
3. 可遍历：数组、对象、字符串(用的很少)、指定次数(用的很少)
