---
title: 字典类的封装
date: 2021-03-02 09:36:24
tag: Algorithm
---

### 字典类
```js
function Dictionary() {
    // 属性
    this.items = {};
    // 添加
    Dictionary.prototype.set = function (key, value) {
        this.items[key] = value;
    }
    // 判断是否有某个Key
    Dictionary.prototype.has = function (key) {
        return this.items.hasOwnProperty(key);
    }
    // 删除
    Dictionary.prototype.remove = function (key) {
        // 如果不存在，直接返回false
        if (!this.has(key)) {
            return false;
        }
        delete this.items[key];
        return true;
    }
    // 根据key获取value
    Dictionary.prototype.get = function (key) {
        return this.has(key) ? this.items[key] : undefined;
    }
    // 获取所有的keys
    Dictionary.prototype.keys = function () {
        return Object.keys(this.items);
    }
    // 获取所有的values
    Dictionary.prototype.values = function () {
        return Object.values(this.items);
    }
    // size
    Dictionary.prototype.size = function () {
        return this.keys.length;
    }
    // clear
    Dictionary.prototype.clear = function () {
        this.items = {};
    }
}
```

### 测试
```js
// 测试
const dic = new Dictionary();
console.log(dic.size());//0
// 新增
dic.set('a', 123);
dic.set('b', 456);
dic.set('c', 789);
console.log(dic.size());//3
// 判断是否有某个key
console.log(dic.has('a'));//true
// 删除
dic.remove('a');
console.log(dic.has('a'));//false
//根据key获取value
console.log(dic.get('b'));//456
// 获取所有的keys
console.log(dic.keys());//['b', 'c']
// 获取所有的values
console.log(dic.values());//[456, 789]
// clear
dic.clear();
console.log(dic.size());//0
console.log(dic.keys());//[]
```