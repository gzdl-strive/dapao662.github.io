---
title: React中createElement和cloneElement区别
date: 2021-02-26 16:46:16
tag: React
---

> `createElement` 和 `cloneElement` 都能够生成可以直接在jsx页面组件中使用的标签语言，但是在生成过程中是存在细微差别的

### createElement

**语法格式**

``` js
React.createElement(
    type,
    [props],
    [...children]
)
```

**说明**
React.createElement(): JSX语法就是用React.createElement()来构建React元素的。他接受三个参数：

1. 第一个参数(type): 可以是一个标签名。如(div、span、a...)或者React组件.
2. 第二个参数(props): 为传入的属性
3. 第三个参数(children): 是组件的子组件 

``` js
const jsx1 = React.createElement('a', {
    id: '1',
    style: {
        color: 'red'
    },
    href: 'http://www.baidu.com'
}, ['hello']);
```

### cloneElement
**语法**
```js
React.cloneElement(
    element, 
    [props],
    [...children]
)
```

**说明**
React.cloneElement()与 React.createElement()相似，不同的是它传入的第一个参数element是一个 React 元素，而不是标签名或组件。新添加的属性会并入原有的属性，传入到返回的新元素中，而旧的子元素将被替换。将保留原始元素的键和引用。
```js
const jsx2 = React.cloneElement(<a></a>, {
    style: {
        color: 'green'
    },
    href: 'http://www.w3school.com'
}, ['w3c'])
```
