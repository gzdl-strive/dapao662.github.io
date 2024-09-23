---
title: Vue3-模板语法、组件Props、非Prop的attribute
date: 2022-03-22 11:22:24
toc: true
---

## 模板语法

- 动态参数

```html
<a v-bind:[attributeName]="url">... </a>
```

- 对动态参数值约定
  动态参数预期会求出一个字符串，`null`例外。这个特殊的`null`值可以用于显式地移除绑定。任何其他非字符串类型的值都将触发一个警告。
  动态参数表达式有一些语法约束，因为某些字符，如空格和引号，放在 HTML attribute 名里是无效的。例如:

```html
<!-- 这会触发一个编译警告 -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

变通的办法是使用没有空格或引号的表达式，或用计算属性替代这种复杂表达式。

```html
<!--
在 DOM 中使用模板时这段代码会被转换为 `v-bind:[someattr]`。
除非在实例中有一个名为“someattr”的 property，否则代码不会工作。
-->
<a v-bind:[someAttr]="value"> ... </a>
```

- JS 表达式
  模板表达式都被放在沙盒中，只能访问一个受限的全局变量列表，如 Math 和 Date。你不应该在模板表达式中试图访问用户定义的全局变量。

## 组件 Props

- 传入一个对象的所有 property 给子组件

```js
post: {
  id: 1,
  title: 'my title'
}
```

下面的模板：

```html
<blog-post v-bind="post"></blog-post>
```

等价于：

```html
<blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
```

- 单项数据流
  所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外变更父级组件的状态，从而导致你的应用的数据流向难以理解

## 非 Prop 的 attribute

一个非 prop 的 attribute 是指传向一个组件，但是该组件并没有相应 **props** 或 **emits** 定义的 attribute。常见的示例包括 class、style 和 id attribute。可以通过 $attrs property 访问那些 attribute。

> 使用 defineComponent 可以定义一个组件
> **要让 TypeScript 正确推断 Vue 组件选项中的类型，需要使用 defineComponent 全局方法定义组件**

```js
import { defineComponent } from "vue";
export default defineComponent({
  //...
});
```
