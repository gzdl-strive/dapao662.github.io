---
title: 第七章-迭代器和生成器(generator)
date: 2020-12-05 13:10:10
tag: js
---

## 迭代器

**这两种类型没有实现迭代器工厂函数**

``` js        
let num = 1; 
let obj = {}; 
console.log(num[Symbol.iterator]); //undefined
console.log(obj[Symbol.iterator]); //undefined

``` 

**这些类型都实现了迭代器工厂函数**
```js
const str = '123';
const arr = [1, 'hello', "il"];
const map = new Map().set('a', 1).set('b',2);
const set = new Set().add('a').add('b');
// let els = document.querySelectorAll('div');
//输出
console.log(str[Symbol.iterator]);//[Function: [Symbol.iterator]]
console.log(arr[Symbol.iterator]);//[Function: values]
console.log(map[Symbol.iterator]);//[Function: entries]
console.log(set[Symbol.iterator]);//[Function: values]
// console.log(els[Symbol.iterator]);
```

**调用这个工厂函数会生成一个迭代器**

``` js
console.log(str[Symbol.iterator]()); //Object [String Iterator] {}
console.log(arr[Symbol.iterator]()); //Object [Array Iterator] {}
console.log(map[Symbol.iterator]()); //[Map Entries] { [ 'a', 1 ], [ 'b', 2 ] }
console.log(set[Symbol.iterator]()); //Set Iterator] { 'a', 'b' }
```

### 迭代器协议

> 迭代器API使用next()方法在可迭代对象中遍历数据, 每次成功调用next(), 都会返回一个IteratorResult对象，IteratorResult对象包含两个属性, done和value

``` js
//可迭代对象
let arr2 = ['foo', 'arr', 'nex'];
//迭代器工厂函数
console.log(arr2[Symbol.iterator]); //[Function: values]
//迭代器
let iter = arr2[Symbol.iterator]();
console.log(iter); //Object [Array Iterator] {}
//执行迭代
console.log(iter.next()); //{ value: 'foo', done: false }
console.log(iter.next()); //{ value: 'arr', done: false }
console.log(iter.next()); //{ value: 'nex', done: false }
console.log(iter.next()); //{ value: undefined, done: true }
```
