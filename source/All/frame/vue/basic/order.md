---
title: 内置指令和自定义指令
toc: true
date: 2021-10-13 16:55:24
---

### 我们学过的指令：
> v-bind	: 单向绑定解析表达式, 可简写为 :xxx
> v-model	: 双向数据绑定
> v-for  	: 遍历数组/对象/字符串
> v-on   	: 绑定事件监听, 可简写为@
> v-if 	 	: 条件渲染（动态控制节点是否存存在）
> v-else 	: 条件渲染（动态控制节点是否存存在）
> v-show 	: 条件渲染 (动态控制节点是否展示)

### v-text
> v-text指令：
>>1. 作用：向其所在的节点中渲染文本内容。
>>2. 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会。

### v-html
>v-html指令：
>>1.作用：向指定节点中渲染包含html结构的内容。
>>2.与插值语法的区别：
    (1).v-html会替换掉节点中所有的内容，{{xx}}则不会。
    (2).v-html可以识别html结构。
>>3.严重注意：v-html有安全性问题！！！！
    (1).在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击。
    (2).一定要在可信的内容上使用v-html，永不要用在用户提交的内容上！

### v-cloak
>v-cloak指令（没有值）：
  >>1.本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉v-cloak属性。
  >>2.使用css配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题。
```html
<style>
  [v-cloak]{
    display:none;
  }
</style>
<div id="root">
  <h2 v-cloak>{{name}}</h2>
</div>
```

### v-pre
>v-pre指令：
  >>1.跳过其所在节点的编译过程。
  >>2.可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译。

### 自定义指令
需求：定义一个v-big指令，和v-text指令类似，但会把绑定的数值放大10倍
需求：定义一个v-fbind指令，和v-bind功能类似，但可以让其所绑定的input元素默认获取焦点

```js
directives: {
  //big函数何时会被调用?
  //1.指令于元素成功绑定时(一上来)
  //2.指令所在的模板被重新解析时
  //简写：bind和update
  big(element, binding) {
    // console.log(this); 注意：this指向window
    // console.log('big被调用了');
    element.textContent = binding.value * 10;
  },
  // fbind(element, binding) {
  //   element.value = binding.value;
  //   element.focus();//为什么不生效？指令时机问题，此时element还没有编译成真实DOM
  // },
  //需要写完整
  fbind: {
    //指令与元素成功绑定时(一上来)
    bind(element, binding) {
      element.value = binding.value;
    },
    //指令所在元素被插入页面时
    inserted(element, binding) {
      element.focus();
    },
    //指令所在的模板被重新解析时
    update(element, binding) {
      element.value = binding.value;
    },
  },
},
```
```js
//定义全局指令，写法和过滤器类似
Vue.directive("fbind2", {
  //指令与元素成功绑定时(一上来)
  bind(element, binding) {
    element.value = binding.value;
  },
  //指令所在元素被插入页面时
  inserted(element, binding) {
    element.focus();
  },
  //指令所在的模板被重新解析时
  update(element, binding) {
    element.value = binding.value;
  },
});
```

### 自定义指令总结：
```html
<!--  
1、定义语法：
  (1)、局部指令：
    * 完整写法             简写(相当于完整写法里只用了bind和update这两个钩子函数)
    new Vue({              new Vue({
      directives: {           directives:{
        指令名：配置对象          指令名:回调函数   
      }                       }
    })                     })
  (2)、全局指令：
    Vue.directive(指令名，配置对象) 或 Vue.directive(指令名， 回调函数)
二、配置对象中常用的3个回调：
  (1)、bind: 指令与元素成功绑定时调用
  (2)、inserted: 指令所在元素被插入页面时调用
  (3)、update: 指令所在模板被重新解析时调用.
三、备注：
  1、指令定义时不加v-,但使用时要加v-
  2、指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名
-->
```