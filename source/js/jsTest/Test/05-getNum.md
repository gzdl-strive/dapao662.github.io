---
title: 获取标签数和出现次数最多三个标签
date: 2021-01-31 20:08:24
tag: js
---

### 获取一个页面有多少个标签

* 1、获取所有标签对象(document.querySelectorAll('*'))
* 2、获取所有标签对象的标签名
* 3、去重(使用Set)
* 4、输出标签数量(.size)

``` js
let nums = new Set([...document.querySelectorAll('*')].map(item => item.tagName)).size;
console.log(nums);
```

### 获取一个页面出现次数最多的三个标签

* 1、先把所有标签列出来
* 2、按出现次数的多少排序
* 3、取前三个

``` js
let ret = [...document.querySelector('*')]
    .map(item => item.tagName)
    // 归并方法reduce接收两个参数(归并函数，起始值)
    // 归并函数可接收4个参数(上一个归并值，当前值，当前项索引，数组本身)
    .reduce((pre, cur) => {
        //pre刚开始为{}
        //如果之前存在这个属性就+1，否则为1
        pre[cur] = pre[cur] ? pre[cur] + 1 : 1;
        return pre;
    }, {});
//此时ret是一个对象->迭代后排序
//使得对象可迭代-》两种方法(Object.values(接收一个对象，返回它们内容的数组)和Object.entries(接收一个对象，返回键/值对数组))
let res = Object.entries(ret).sort((a, b) => b[1] - a[1]);
console.table(res.spilce(0, 3));
```
