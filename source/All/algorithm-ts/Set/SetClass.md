---
title: 集合类
date: 2022-07-04 10:17:24
toc: true
---

一、集合常见操作
add(value): 向集合添加一个新的项
remove(value):从集合中移除一个值
has(value): 如果值在集合中,返回true,否则返回false
clear(): 移除集合中的所有项
size(): 返回集合所包含元素的数量.与数组的Length属性类似
values(): 返回一个包含集合中所有值的数组

二、集合间操作
并集: 对于给定的两个集合，返回一个包含两个集合所有元素的新集合
交集：对于给定的两个集合，返回一个包含两个集合中共有元素的新集合
差集：对于给定的两个集合，返回一个包含所有存在于第一个集合且不存在于第二个集合中的元素的新集合
子集：验证一个给定集合是否属于另一个集合的子集

## 集合中用到的类型
```ts
export type SetNode = string | number;
export type SetType = Record<SetNode, SetNode>;
```

## 集合类
```ts
class Set {
  items: SetType;
  constructor() {
    this.items = {};
  }
  // 一、集合常见操作
  // 1、add方法
  add(value: SetNode) {
    // 因为集合中不能含有重复元素，所以在插入之前需要判断一下是否有该元素
    if (this.has(value)) {
      return false;
    }
    // 将该元素添加到集合中
    this.items[value] = value;
    return true;
  }
  // 2、remove方法
  remove(value: SetNode) {
    // 判断一下集合中是否存在待删除元素
    if (!this.has(value)) {
      return false;
    }
    // 将元素从集合中移除
    delete this.items[value];
    return true;
  }
  // 3、has方法
  has(value: SetNode) {
    return this.items.hasOwnProperty(value);
  }
  // 4、clear方法
  clear() {
    // 置为初始值即可
    this.items = {};
  }
  // 5、size方法
  size() {
    return Object.keys(this.items).length;
  }
  // 6、获取集合中所有值
  values() {
    return Object.keys(this.items);
  }
  // 二、集合间操作
  // 1、union并集
  union(otherSet: Set) {
    // this -> 当前集合对象A
    // otherSet -> 集合对象B
    // 1、创建一个新集合
    let unionSet = new Set();
    // 2、将A集合中所有的元素添加到新集合中
    let values = this.values();
    for (let i = 0; i < values.length; i++) {
      unionSet.add(values[i]);
    }
    // 3、取出B集合中的元素，将它添加到新集合中
    values = otherSet.values();
    for (let i = 0; i < values.length; i++) {
      unionSet.add(values[i]);
    }
    return unionSet;
  }
  // 2、intersection交集
  intersection(otherSet: Set) {
    //1、创建一个新集合
    const intersectionSet = new Set();
    //2、从A中取出一个个元素，判断是否同时存在于集合B中，存放进新集合中
    const values = this.values();
    for (let i = 0; i < values.length; i++) {
      if (otherSet.has(values[i])) {
        intersectionSet.add(values[i]);
      }
    }
    return intersectionSet;
  }
  // 3、difference差集
  difference(otherSet: Set) {
    // 1、创建新集合
    const differenceSet = new Set();
    // 2、取出A中的一个个元素，如果不存在于B中，则加入新集合
    const values = this.values();
    for (let i = 0; i < values.length; i++) {
      if (!otherSet.has(values[i])) {
        differenceSet.add(values[i]);
      }
    }
    return differenceSet;
  }
  // 4、sub
  sub(otherSet: Set) {
    // 遍历A中所有元素，如果A中存在B没有的元素，返回false
    // 遍历完没有返回false,则返回true
    const values = this.values();
    for (let i = 0; i < values.length; i++) {
      if (!otherSet.has(values[i])) {
        return false;
      }
    }
    return true;
  }
}
```

## 测试
```ts
import Set from './Set';

const set = new Set();

console.log(`size: ${set.size()}`);

// 1、add方法
console.log(set.add('abc'));//true
console.log(set.add('abc'));//false
console.log(set.add('def'));//true
console.log(set.add('gif'));//true
console.log('add: ', set.values());//[ 'abc', 'def', 'gif' ]

// 2、remove方法
console.log(set.remove('abc'));//true
console.log('remove: ', set.values());//remove:  [ 'def', 'gif' ]

// 3、has方法
console.log(set.has('abc'));//false
console.log(set.has('def'));//true

// 4、size,clear
console.log(set.size());//2
set.clear();
console.log(set.size());//0

console.log('=============');


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
const subset = setA.sub(setB);
console.log(`subset: ${subset}`);//subset: false
const setC = new Set();
setC.add('def');
const subset2 = setC.sub(setA);
console.log(`subset2: ${subset2}`);//subset2: true
```