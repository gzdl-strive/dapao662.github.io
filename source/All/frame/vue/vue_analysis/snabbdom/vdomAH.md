---
title: 虚拟DOM和h函数
date: 2022-01-09 11:11:24
toc: true
---

>虚拟DOM: 用JavaScript对象描述DOM的层次结构。DOM中的一切书写都在虚拟DOM中有对应的属性。
>DOM如何变为虚拟DOM，属于模板编译原理范畴，本章节不做研究

## 研究内容
- 1、虚拟DOM如何被渲染函数(h函数)产生？
  手写h函数
- 2、diff算法原理
  手写diff算法
- 3、虚拟DOM如何通过diff变成真正的DOM的
  事实上，虚拟DOM变回真正的DOM,是涵盖在diff算法里面的

## h函数
- h函数用来产生虚拟节点(vnode)
调用h函数：
```js
h('a', { props: { href: "http://www.baidu.com" } }, '百度');
```
得到这样的虚拟节点
```json
{
  "sel": "a",
  "data": {
    "props": {
      "href": "http://www.baidu.com"
    }
  },
  "text": "百度"
}
```
它表示真正的DOM节点：
`<a href="http://www.baidu.com">百度</a>`

**一个虚拟节点有哪些属性**
```js
{
  children: undefined,
  data: {},
  elm: undefined,
  key: undefined,
  sel: "div",
  text: "我是谁"
}
```

>h函数可以嵌套使用，从而得到虚拟DOM树