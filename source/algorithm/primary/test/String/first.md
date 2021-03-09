---
title: 反转字符串
date: 2021-3-04 10:37:24
tag: Algorithm
---

### 反转字符串
>题目描述：编写一个函数，其作用是将输入的字符串反转过来。
输入字符串以字符数组 char[] 的形式给出。
不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

你可以假设数组中的所有字符都是 ASCII 码表中的可打印字符。
**示例**
示例 1：
输入：["h","e","l","l","o"]
输出：["o","l","l","e","h"]
示例 2：
输入：["H","a","n","n","a","h"]
输出：["h","a","n","n","a","H"]

### 方法一：使用双指针
```js
const reverseString = function (s) {
    const len = s.length;
    let p = 0, q = len - 1;
    while (p < q) {
        [s[p], s[q]] = [s[q], s[p]];
        p++;
        q--;
    }
    return s;
}
const arr1 = ['h', 'e', 'l', 'l', 'o'];
const res1 = reverseString(arr1);
console.log(res1);//[ 'o', 'l', 'l', 'e', 'h' ]
```