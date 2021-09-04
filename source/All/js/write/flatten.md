---
title: 数组扁平化
date: 2021-09-03 11:18:24
toc: true
---

把如下数组扁平化处理
```js
let arr = [1, 2, [3, 4, [5, [6]]]];
```
**扁平化后**
```js
[1, 2, 3, 4, 5, 6];
```

### 方法一、flat方法
```js
const res = arr.flat(Infinity);
```

### 方法二、正则匹配
```js
const res2 = JSON.stringify(arr).replace(/\[|\]/g, '').split(',');
//问题：数据类型都变为字符串
//[ '1', '2', '3', '4', '5', '6' ]
```

### 方法三、正则改良版本
```js
const res3 = JSON.parse('[' + JSON.stringify(arr).replace(/\[|\]/, '') + ']');
```

### 方法四、使用递归
递归遍历，判断当前元素是否是数组，如果是数组继续处理，否则之间添加到新数组中
```js
const res4 = [];
const fn = arr => {
  for (let i = 0; i < arr.length; i++) {
    if (Array.isArray(arr[i])) {
      fn(arr[i]);
    } else {
      res4.push(arr[i]);
    }
  }
}
```

### 方法五、reduce + 递归
```js
const flatten = arr => {
  return arr.reduce(
    (pre, cur) => {
      return pre.concat(Array.isArray(cur) ? flatten(cur) : cur);
    }, [])
}
```