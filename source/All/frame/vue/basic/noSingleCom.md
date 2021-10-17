---
title: 非单文件组件
date: 2021-10-13 17:14:24
toc: true
---

>Vue中使用组件的三大步骤：
>>一、定义组件(创建组件)
>>二、注册组件
>>三、使用组件(写组件标签)

### 一、如何定义一个组件？
使用`Vue.extend(options)`创建，其中`options`和`new Vue(options)`时传入的那个options几乎一样，但也有点区别；
**区别如下：**
- 1.el不要写，为什么？ ——— 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器。
- 2.data必须写成函数，为什么？ ———— 避免组件被复用时，数据存在引用关系。
**备注：使用template可以配置组件结构。**

二、如何注册组件？
      1.局部注册：靠new Vue的时候传入components选项
      2.全局注册：靠Vue.component('组件名',组件)

三、编写组件标签：
`<school></school>`

### 二、几个注意点
1.关于组件名:
  一个单词组成：
        第一种写法(首字母小写)：school
        第二种写法(首字母大写)：School
  多个单词组成：
        第一种写法(kebab-case命名)：my-school
        第二种写法(CamelCase命名)：MySchool (需要Vue脚手架支持)
  备注：
      (1).组件名尽可能回避HTML中已有的元素名称，例如：h2、H2都不行。
      (2).可以使用name配置项指定组件在开发者工具中呈现的名字。

2.关于组件标签:
  第一种写法：<school></school>
  第二种写法：<school/>
  备注：不用使用脚手架时，<school/>会导致后续组件不能渲染。

**3.一个简写方式：**
`const school = Vue.extend(options)` 可简写为：`const school = options`

### VueComponent
关于VueComponent：
>1.school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的。
>2.我们只需要写`<school/>或<school></school>`，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：`new VueComponent(options)`。
>3.**特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！！！！**
>4.关于this指向：
    (1).组件配置中：
          data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【VueComponent实例对象】。
    (2).new Vue(options)配置中：
          data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是【Vue实例对象】。
>5.VueComponent的实例对象，以后简称vc（也可称之为：组件实例对象）。
  Vue的实例对象，以后简称vm。

### 一个重要的内置关系
>1.一个重要的内置关系：`VueComponent.prototype.__proto__ === Vue.prototype`
>2.为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue原型上的属性、方法。
		