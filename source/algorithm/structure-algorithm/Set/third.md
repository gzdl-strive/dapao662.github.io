---
title:  集合间操作
date: 2021-02-26 11:17:24
tag: Algorithm
---

## 集合间操作

集合间通常由如下操作：
* 并集：对于给定的两个集合，返回一个包含两个集合所有元素的新集合
* 交集：对于给定的两个集合，返回一个包含两个集合中共有元素的新集合
* 差集：对于给定的两个集合，返回一个包含所有存在于第一个集合且不存在于第二个集合的元素的集合
* 子集：验证一个给定集合是否是另一个集合的子集

## 集合间操作类

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

    // 4、clear()方法
    Set.prototype.clear = function () {
        this.items = {};
    }

    // 5、size()方法
    Set.prototype.size = function () {
        return Object.keys(this.items).length;
    }

    // 6、获取集合中所有值
    Set.prototype.values = function () {
        return Object.keys(this.items);
    } 

    // 集合间操作
    // 1、union并集
    Set.prototype.union = function (otherSet) {
        // this: 集合对象A
        // otherSet: 集合对象B
        // 1、创建新集合
        let unionSet = new Set();
        // 2、将A集合中所有的元素添加到新集合中
        let values = this.values();
        for (let i = 0; i < values.length; i++) {
            unionSet.add(values[i]);
        }
        // 3、取出B集合中的元素，判断是否需要添加到新集合中
        values = otherSet.values();
        for (let i = 0; i < values.length; i++) {
            unionSet.add(values[i]);
        }
        return unionSet;
    }

    // 2、交集intersection
    Set.prototype.intersection = function (otherSet) {
        // 1、创建新集合
        const intersectionSet = new Set();
        // 2、从A中取出一个个元素，判断是否同时存在于集合B中，存在放入新集合中
        let values = this.values();
        for (let i = 0; i < values.length; i++) {
            let item = values[i];
            if (otherSet.has(item)) {
                intersectionSet.add(item);
            }
        }
        return intersectionSet;
    }

    // 3、差集
    Set.prototype.difference = function (otherSet) {
        // 1、创建新集合
        const differenceSet = new Set();
        // 2、取出A集合中一个个元素，判断是否同时不存在于B中,则添加到新集合中
        let values = this.values();
        for (let i = 0; i < values.length; i++) {
            const item = values[i];
            if (!otherSet.has(item)) {
                differenceSet.add(item);
            }
        }
        return differenceSet;
    }

    // 4、子集
    Set.prototype.subset = function (otherSet) {
        // 遍历集合A中的所有元素，如果集合A中的元素，在集合B中不存在，那么false
        // 如果遍历整个集合，依然没有返回false,那么返回true
        const values= this.values();
        for (let i = 0; i < values.length; i++) {
            const item = values[i];
            if (!otherSet.has(item)) {
                return false;
            }
        }
        return true;
    }
}
```

## 测试
```js
const setA = new Set();
setA.add('abc');
setA.add('def');
setA.add('gif');
const setB = new Set();
setB.add('def');
setB.add('nba');
// 并集
const unionSet = setA.union(setB);
console.log(`union: ${unionSet.values()}`);//union: abc,def,gif,nba
// 交集
const intersectionSet = setA.intersection(setB);
console.log(`intersection: ${intersectionSet.values()}`)//intersection: def
// 差集
const differenceSet1 = setA.difference(setB);
const differenceSet2 = setB.difference(setA);
console.log(`A-diff-B: ${differenceSet1.values()}`); //A-diff-B: abc,gif
console.log(`B-diff-A: ${differenceSet2.values()}`); //B-diff-A: nba
// 子集
const subset = setA.subset(setB);
console.log(`subset: ${subset}`);//subset: false
const setC = new Set();
setC.add('def');
const subset2 = setC.subset(setA);
console.log(`subset2: ${subset2}`);//subset2: true
```