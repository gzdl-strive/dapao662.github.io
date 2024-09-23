---
title: 递归实现render
date: 2021-08-14 13:10:02
toc: true
---

>前面我们已经实现了简易的createElement函数，现在我们要做的就是初步实现一个ReactDOM.render函数。
该函数接收两个参数，第一个参数就是createElement函数的返回值，第二个参数是我们要挂载的节点.
`ReactDOM.render(element, container)`

**一开始，我们只关心增加节点的操作，后面的话会使用到删除和更新节点的操作**
```js
function render(element, container) {
  //TODO create dom nodes
}
```

element入参是我们通过createElement函数返回的一个对象，用于构造真实DOM,该对象结构如下
![1](/assets/reactImg/my_react_1.png "1")

### 首先我们通过element的type属性创建dom,然后添加到容器container上
```js
function render(element, container) {
  const dom = document.createElement(element.type);

  container.appendChild(dom);
}
```

### 接着dom节点应该有自己的属性，我们也要添加上去
**属性保存在props属性中，需要注意的是，props属性中还可能存在children字段用于保存子节点，所以我们给当前节点添加属性时需要去掉children**

下面我们会用到几个函数
>1、Object.keys: Object.keys() 方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致 。

>2、filter filter() 方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。
  >> filter() 不会改变原数组
  >> filter() 不会对空数组进行检测

>3、forEach forEach() 方法对数组的每个元素执行一次给定的函数

```js
function render(element, container) {
  const dom = document.createElement(element.type);

  //定义过滤函数，这边使用filter函数
  //过滤掉属性名为'children'的属性
  const isProperty = key => key !== 'children';
  
  Object.keys(element.props)
    .filter(isProperty)
    .forEach(name => {
      dom[name] = element.props[name];
    });

  container.appendChild(dom);
}
```
现在我们已经该当前节点添加上属性了，但是我们还没有处理子节点，所以接下来需要处理一下子节点.**我们可以递归调用render函数来处理子节点**

### 递归子节点
```js
function render(element, container) {
  const dom = document.createElement(element.type);
  const isProperty = key => key !== 'children';
  Object.keys(element.props)
    .filter(isProperty)
    .forEach(name => {
      dom[name] = element.props[name];
    });
  
  //处理子节点
  element.props.children.forEach(child => (
    render(child, dom);
  ));

  container.appendChild(dom);
}
```
上述代码我们已经成功处理了子节点，但是我们忽略了一个重要的问题，我们的节点都是通过`document.createElement`创建的，我们的节点可能存在文本节点，而文本节点的创建方式不同于元素节点。

### 处理文本节点
**我们先前创建的createElement函数中，把文本节点的类型命名为`TEXT_ELEMENT`**
```js
function render(element, container) {
  // 移除以下代码
  // const dom = document.createElement(element.type);
  //新增如下代码:根据元素类型生成不同类型dom节点
  const dom = element.type === 'TEXT_ELEMENT' ? document.createTextNode('') : document.createElement(element.type);
  const isProperty = key => key !== 'children';
  Object.keys(element.props)
    .filter(isProperty)
    .forEach(name => {
      dom[name] = element.props[name];
    });
  
  //处理子节点
  element.props.children.forEach(child => (
    render(child, dom);
  ));

  container.appendChild(dom);
}
```
**这边之所以使用`document.createTextNode('')`来创建文本节点，是因为，可以和使用`document.createElement`使用共同的代码，我们也是通过往节点上添加属性的方式来创建文本节点。**

至此，我们的`ReactDOM.render()`函数的递归实现已经写好了。

