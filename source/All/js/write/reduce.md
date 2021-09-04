---
title: reduce
date: 2021-09-03 14:38:24
toc: true
---

## reduce方法介绍
```js
arr.reduce(function (prev, cur, index, arr) {
  ...
}, init);
```

- prev: 表示上一次调用回调时的返回值，或者初始值init;
- cur: 表示当前正在处理的数组元素
- index: 表示当前正在处理的数组元素的索引，若提供init值，则索引为0，否则索引为1.
- arr: 表示原数组

- init: 表示初始值

## 常用于
>- 数组求和，求乘积
```js
let arr = [1, 2, 3, 4];
let sum = arr.reduce((pre, cur) => {
  return pre + cur;
});
console.log(sum);//10
```

>- 计算数组中每个元素出现的次数
```js
let arr = ['小米', '华为', '苹果', '一加', '一加', '一加', '小米'];
const newArr = arr => {
  return arr.reduce((pre, cur) => {
    (cur in pre) ? pre[cur] += 1 : pre[cur] = 1;
    return pre;
  }, {});
}
const result = newArr(arr);
console.log(result);//{ '小米': 2, '华为': 1, '苹果': 1, '一加': 3 }
```

>- 数组去重
```js
const arr = [1, 1, 2, 3, 5, 7, 2, 9];
const res = arr.reduce((pre, cur) => {
  // if (pre.indexOf(cur) === -1) return pre.concat(cur);
  if (!pre.includes(cur)) return pre.concat(cur);
  return pre;
}, []);

console.log(res);//[ 1, 2, 3, 5, 7, 9 ]
```

>- 将二维数组转化成一维数组(数组扁平化-配合递归)
>- 将多维数组转化成一维数组(数组扁平化-配合递归)
>- 对象里的属性求和
