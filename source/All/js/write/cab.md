---
title: call、apply、bind
date: 2021-09-03 16:06:24
toc: true
---

## 应用
### 1、将 伪数组 转化成 数组（通过slice方法）
伪数组： arguments对象、dom节点、包含length的对象
```js
let div = document.getElementsByTagName('div');
let arr = Array.prototype.slice.call(div);
//通过slice方法把类数组转成数组，能转化的重点在于slice方法
```
**slice方法只能兼容ie8以上**

我们可以自己封装一个方法
```js
function listToArray(likeArray) {
  let ary = [];
  try {
    //使用try catch捕获，如果能使用slice方法就用
    ary = Array.prototype.slice.call(likeArray);
  } catch {
    //否则使用for循环一个个添加
    for (let i = 0; i < likeArray.length; i++) {
      ary[ary.length] = likeArray[i];
    }
  }
}
```

### 2、数组的拼接
```js
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
let res = Array.prototype.push.apply(arr1, arr2);
console.log(res);//[1, 2, 3, 4, 5, 6]
```

### 3、判断数据类型
```js
let arr3 = [1, 2, 3];
function isArray(array) {
  return Object.prototype.toString.call(array) === '[object Array]'
}
console.log(isArray(arr3));//true
```

