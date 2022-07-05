---
title: 字典类
date: 2022-07-05 10:59:24
toc: true
---

## 需要用到的类型
```ts
export type DictionnaryNode = string | number;
export type DictionnaryType = Record<DictionnaryNode, DictionnaryNode>;
```

## 字典类
```ts
import { DictionnaryNode, DictionnaryType } from './types';

class Dictionnary {
  items: DictionnaryType;
  constructor() {
    this.items = {};
  }
  // 新增
  set(key: DictionnaryNode, value: any) {
    this.items[key] = value;
  }
  // 判断是否存在某个key
  has(key: DictionnaryNode) {
    return this.items.hasOwnProperty(key);
  }
  // 删除
  remove(key: DictionnaryNode) {
    if (!this.has(key)) {
      return false;
    }
    delete this.items[key];
    return true;
  }
  // 根据key获取value
  get(key: DictionnaryNode) {
    if (!this.has(key)) return null;
    return this.items[key];
  }
  // 获取所有key
  keys() {
    return Object.keys(this.items);
  }
  // 获取所有value
  values() {
    return Object.values(this.items) || [];
  }
  // size
  size() {
    return this.keys().length;
  }
  // clear
  clear() {
    this.items = {};
  }
}

export default Dictionnary;
```

## 测试
```ts
import Dictionnary from "./Dictionnary";

// 测试
const dic = new Dictionnary();
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

