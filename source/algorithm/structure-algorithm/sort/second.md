---
title: 封装列表类
date: 2021-03-10 09:34:24
tag: Algorithm
---

**在开始排序前，我们先创建一个列表封装我们的数据项**
```js
//封装列表类
function ArrayList() {
    //属性
    this.array = [];
    //方法
    //将我们的数据插入到数组中
    ArrayList.prototype.insert = function (data) {
        this.array.push(data);
    }
    //toString方法
    ArrayList.prototype.toString = function () {
        return this.array.join('-');
    }
}
```

**测试**
```js
//测试
let list = new ArrayList();

list.insert(10);
list.insert(22);
list.insert(55);

console.log(list.toString());//10-22-55
```