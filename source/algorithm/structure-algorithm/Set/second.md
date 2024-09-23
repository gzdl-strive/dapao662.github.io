---
title:  集合常见操作
date: 2021-02-26 11:08:24
tag: Algorithm
---

### 集合常见的操作
>add(value): 向集合添加一个新的项
remove(value):从集合中移除一个值
has(value): 如果值在集合中,返回true,否则返回false
clear(): 移除集合中的所有项
size(): 返回集合所包含元素的数量.与数组的Length属性类似
values(): 返回一个包含集合中所有值的数组
还有一些集合其他相关的操作,暂时不用太多,这里暂不封装

### 集合类
```js
function Set() {
    // 属性
    this.items = {};
    // 方法
    // 1、add方法
    Set.prototype.add = function (value) {
        // 判断当前集合中是否已经包含了该元素
        if (this.has(value)) {
            return false;
        }
        // 将元素添加到集合中
        this.items[value] = value;
        return true;
    }
    // 2、remove方法
    Set.prototype.remove = function (value) {
        // 1、判断该集合中是否包含该元素
        if (!this.has(value)) {
            return false;
        }
        // 2、将元素从属性中删除
        delete this.items[value];
        return true;
    }
    // 3、has方法
    Set.prototype.has = function (value) {
        return this.items.hasOwnProperty(value);
    }
    // 4、clear方法
    Set.prototype.clear = function () {
        this.items = {};
    }
    // 5、size方法
    Set.prototype.size = function () {
        return Object.keys(this.items).length;
    }
    // 6、获取集合中所有值
    Set.prototype.values = function () {
        return Object.keys(this.items);
    }
}
```

### 测试
```js
let set = new Set();

// 1、add方法
console.log(set.add('abc'));//true
console.log(set.add('abc'));//false
console.log(set.add('def'));//true
console.log(set.add('gif'));//true
console.log('add: ',set.values());//[ 'abc', 'def', 'gif' ]

// 2、remove方法
console.log(set.remove('abc'));//true
console.log('remove: ',set.values());//remove:  [ 'def', 'gif' ]

// 3、has方法
console.log(set.has('abc'));//false
console.log(set.has('def'));//true

// 4、size,clear
console.log(set.size());//2
set.clear();
console.log(set.size());//0
```