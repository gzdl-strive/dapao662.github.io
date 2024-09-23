---
title: ES11
date: 2020-11-12 16:33:02
tag: js
---

### matchAll

>`matchAll()` 方法返回一个包含所有匹配正则表达式的结果的迭代器。使用 `for...of` 遍历或者使用 操作符 `...` `Array.from` 将其转换成数组。

```js
const reg = /[0-3]/g;
const data = '2020';
console.log([...data.matchAll(reg)]);

/*Compiler to:*/
[
  [ '2', index: 0, input: '2020', groups: undefined ],
  [ '0', index: 1, input: '2020', groups: undefined ],
  [ '2', index: 2, input: '2020', groups: undefined ],
  [ '0', index: 3, input: '2020', groups: undefined ] 
]
```
