---
title: 虚拟DOM与Diff
date: 2022-01-06 16:00:24
toc: true
---

## 简单介绍下虚拟DOM和diff算法
>[简单介绍虚拟DOM和diff](/All/frame/vue/vue_analysis/snabbdom/introduction "介绍虚拟DOM和diff")

## snabbdom简介和搭建测试环境
>[snabbdom简介和搭建环境](/All/frame/vue/vue_analysis/snabbdom/environment "snabbdom简介和搭建环境")

## 虚拟DOM和h函数
>[VDOM和h函数](/All/frame/vue/vue_analysis/snabbdom/vdomAH "虚拟DOM和h函数")

## 手写snabbdom
### 1、手写h函数
>[h函数](/All/frame/vue/vue_analysis/snabbdom/h "手写h函数")
### 2、patch函数
>- key很重要。**key是这个节点的唯一标识，告诉diff算法，在更改前后他们是同一节点**
>- 只有是同一个虚拟节点，才进行精细化比较，否则就是暴力删除旧的、插入新的。延伸问题：如何定义是同一个虚拟节点？答：**选择器相同且key相同**。
>- 只进行同层比较，不会进行跨层比较。即使是同一片虚拟节点，但是跨层了，对不起，精细化比较不diff你，而是暴力删除旧的、然后插入新的

>[处理不是同一节点时](/All/frame/vue/vue_analysis/snabbdom/patch1 "处理不是同一节点时")
>[处理新旧节点是同一节点](/All/frame/vue/vue_analysis/snabbdom/patch2 "处理同一节点")