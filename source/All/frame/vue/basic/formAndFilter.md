---
title: 收集表单数据和过滤器
toc: true
date: 2021-10-13 16:50:24
---

### 收集表单数据：
  若：`<input type="text"/>`，则v-model收集的是value值，用户输入的就是value值。
  若：`<input type="radio"/>`，则v-model收集的是value值，且要给标签配置value值。
  若：`<input type="checkbox"/>`
    1.没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
    2.配置input的value属性:
      (1)v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
      (2)v-model的初始值是数组，那么收集的的就是value组成的数组
  备注：v-model的三个修饰符：
    lazy：失去焦点再收集数据
    number：输入字符串转为有效的数字
    trim：输入首尾空格过滤

### 过滤器
>定义：对要显示的数据进行特定格式化后再显示(适用于一些简单逻辑的处理)
语法：
  1.注册过滤器：`Vue.filter(name, callback)` 或 `new Vue({ filters: {}})`
  2.使用过滤器：`{{ xxx | 过滤器名 }}` 或 `v-bind属性="xxx | 过滤器名"`
备注：
  1.过滤器也可以接受额外参数，多个过滤器也可以串联
  2.并没有改变原本的数据，是产生新的对应的数据
```html
    <!-- 过滤器实现 -->
    <h2>过滤器实现: {{ time | timeFormatter}}</h2>
    <!-- 过滤器实现传参 -->
    <h2>过滤器实现: {{ time | timeFormatter('YYYY-MM-DD') | mySlice}}</h2>
    <!-- 全局过滤器 -->
    <h2>全局过滤器: {{ time | timeFormatter | mySlice2}}</h2>
    <!-- 过滤器还可以用于v-bind -->
    <h3 :x="msg | mySlice2">s1mple</h3>
```