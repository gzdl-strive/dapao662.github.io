---
title: 等号左边的变量提升
date: 2021-03-10 15:45:24
tag: js
---

### 普通函数下变量提升示例
```js
print();//666
function print() {
    console.log('666');
}
print();//666
```
**很显然上面都输出的666,因为带function的已经进行了变量提升**

### 匿名函数下的带=的变量提升
```js
print();//TypeError: print is not a function
var print = function () {
    console.log('print');
}
```
>思路：同样由于变量提升机制带var的print是一开始值是undefined,所以print()这时还不是一个函数，所有报类型错误TypeError

![3](/assets/jsImg/variablePromotion/3.png "3")