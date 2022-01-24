---
title: 手写h函数
date: 2022-01-09 11:39:24
toc: true
---

>编写一个低配版本的h函数，这个函数必须接收3个参数，缺一不可，相当于它的重载功能比较弱
>也就是说调用的时候形态必须是下面的三种之一
- 形态1 `h('div', {}, '文字')`
- 形态2 `h('div', {}, [])`
- 形态3 `h('div', {}, h())`

## vnode函数
>函数功能十分简单，就是把传入的参数组合成对象返回出去
```js
//vnode.js
const vnode = (sel, data, children, text, elm) => {
  const key = data.key;
  return {
    sel,
    data,
    children,
    text, 
    elm,
    key
  }
}
export default vnode;
```

## h函数
>通过调用vnode函数，生成对应虚拟节点
>接收三个参数：我们是低配版，必须传3个,通过判断第三个参数的类型来做不同的处理
```js
function vnode(sel, data, c) {
  //检查参数个数
  if (arguments.length !== 3) {
    throw new Error('对不起，h函数传入的参数个数必须是3个')
  }

  //检查参数c的类型
  if (typeof c === 'string' || typeof c === 'number') {
    //形态1
    return vnode(sel, data, undefined, c, undefined);
  } else if (Array.isArray(c)) {
    //形态2
    let children = [];
    //遍历c,收集children
    for (let i = 0; i < c.length; i++) {
      //检查c[i]必须是对象
      if (!(typeof c[i] === 'object' && c[i].hasOwnProperty('sel'))) {
        throw new TypeError('传入的数组参数中某项不是h函数');
      }
      //这里不用执行c[i],因为测试语句中已经有了执行
      //此时只需要收集即可
      children.push(c[i]);
    }
    //循环结束，收集完毕，返回虚拟节点
    return vnode(sel, data, children, undefined, undefined);
  } else if (typeof c === 'object' && c.hasOwnProperty('sel')) {
    //形态3
    let children = [c];
    return vnode(sel, data, children, undefined, undefined);
  } else {
    throw new TypeError('传入的第三个参数类型不正确')
  }
}

export default h;
```
结果如下
![1](/assets/vueImg/vueSource/snabbdom2.png "h")
