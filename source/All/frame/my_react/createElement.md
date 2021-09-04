---
title: createElement
date: 2021-08-13 16:44:02
toc: true
---

>目标,创建一个createElement函数，接收参数: tag、props、children(可能是多个)

把
```js
const element = createElement(
  'div',
  {
    id: 'container',
  },
  createElement('h1', null, 'Hello'),
  createElement('h2', null, 'World!'),
)
```
转换成：
```js
{
  type: 'div',
  props: {
    id: 'container',
    children: [
      {
        type: 'h1',
        props: {
          children: 'Hello'
        }
      },
      {
        type: 'h2',
        props: {
          children: 'World!'
        }
      }
    ]
  }
}
```

**createElement.js**
```js
//chidlren数量不确定，使用剩余参数
function createElement(type, props, ...children) {
  //返回一个对象
  return {
    type,
    props: {
      ...props,
      children: children.map(name => {
        typeof name === 'object' ? name : createTextElement(name)
      })
    }
  }
}
//生成文本节点
function createTextElement(text) {
  return {
    type: 'TEXT_ELEMENT',
    props: {
      nodeValue: text,
      children: []
    }
  }
}

export default createElement;
```
