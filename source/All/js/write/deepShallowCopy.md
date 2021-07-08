---
title: 深浅拷贝
date: 2021-07-06 22:55:24
toc: true
---

### 前置知识
>* js的一般数据类型的存储(栈内存)
>* js的引用类型的数据存储->栈内存存储一个地址(这个地址指向堆内存)

## 浅拷贝 深拷贝
- 浅拷贝就是创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。**如果属性值是基本类型，拷贝的就是基本类型的值**。**如果属性值是引用类型，拷贝的就是内存地址，所以如果其中一个对象改变了这个地址，就会影响到另一个对象。**
- 深拷贝就是将一个对象从内存中完整的拷贝一份出来，从堆内存中开辟一个新的区域用于存放新对象，且修改新对象不会影响原对象。

## 赋值、浅拷贝、深拷贝的区别（针对引用类型来说）
1. 赋值：当我们把一个对象赋值给一个新的变量时，赋的其实是该对象在栈中的地址，而不是在堆中的数据。也就是两个对象指向同一个存储空间，无论哪个对象发生了改变，其实就是改变了存储空间的内容，因此，两个对象是联动的。
2. 浅拷贝：重新在堆中创建内存，拷贝前后对象的基本数据类型相互不影响，但拷贝前后对象的引用类型因共享同一块内存，会相互影响。
3. 深拷贝：从堆内存中开辟一块新的区域用于存放新对象，对对象中的子对象进行递归拷贝，拷贝前后的两个对象互不影响。

```js
let person = {
  name: 'Simple',
  weapon: ['AWP', ['AK', 'M4A1'], 'USP'],
  date: new Date(),
  reg: new RegExp(/^5/),
  function () {},
}
```

### 赋值
```js
let person1 = person;
person1.name = 'electronic';
person1.weapon[0] = 'AK47';

console.log(person);
/*
{
  name: 'electronic',//改变了
  weapon: ['AK47', ...],//改变了
  ...
}
*/
console.log(person1);
/*
{
  name: 'electronic',
  weapon: ['AK47', ...],
  ...
}
*/
```

### 浅拷贝
```js
function shallowCopy(obj) {
  let target = {};
  for (let i in obj) {
    if (obj.hasOwnProperty(i)) {
      target[i] = obj[i];
    }
  }
  return target;
}
let person1 = shallowCopy(person);
person1.name = 'electronic';
person1.weapon[0] = 'AK47';

console.log(person);
/*
{
  name: 'Simple',//没改变
  weapon: ['AK', ...],//改变了
  ...
}
*/
console.log(person1);
/*
{
  name: 'electronic',
  weapon: ['AK', ...],
  ...
}
*/
```

### 深拷贝
**方法一：`JSON.parse(JSON.stringify(obj))`**
>缺点：只能对支持`JSON`格式的数据类型进行深拷贝，不支持JSON的格式会出现错误(例如：funciton、Date、RegExp)

**方法二：递归**
```js
function deepClone(obj) {
  let target = {};
  if (typeof obj !== "object") return obj;
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);
  for (let i in obj) {
    if (obj.hasOwnProperty(i)) {
      target[i] = deepClone(obj[i]);
    }
  }
  return target;
}
```

## 总结
| | 和原数据是否指向同一对象 | 第一次数据为一般数据类型 | 第一层数据不是一般数据类型 |
|:------:|:-----:|:------:|:------:|
| 赋值 | 是 | 改变会使原始数据一同改变 | 改变会使原始数据一同改变 |
| 浅拷贝 | 否 | 改变不会使原始数据一同改变 | 改变会使原始数据一同改变 |
| 深拷贝 | 否 | 改变不会使原始数据一同改变 | 改变不会使原始数据一同改变 |
