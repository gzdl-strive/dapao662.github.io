---
title: 验证回文串
date: 2021-03-05 11:12:24
tag: Algorithm
---

### 验证回文串
>题目描述：给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写

**说明**
本题中，我们将空字符串定义为有效的回文串

**示例**
示例 1:
输入: "A man, a plan, a canal: Panama"
输出: true
示例 2:
输入: "race a car"
输出: false

### 解法一：排序
```js
const isPalindrome = function (s) {
    let str = s.toLocaleLowerCase().match(/[a-zA-Z0-9]+/g);//数组=>转成字符串
    //如果是空字符串就返回true
    if (!str) {
        return true;
    }
    let str2 = str.join('');//转成字符串
    const len = str2.length;
    let p = 0, q = len - 1;
    while (p < q) {
        if (str2[p] !== str2[q]) {
            return false;
        }
        p++;
        q--;
    }
    return true;
}
```

**改进版**
```js
const isPalindrome = function (s) {
    const str = s.replace(/[a-zA-Z0-9]/, '').toLocaleLowerCase();
    if (!str) {
        return true;
    }
    let len = str.length, p = 0, q = len - 1;
    while (p < q) {
        if (str[p] !== str[q]) {
            return false;
        }
        p++;
        q--;
    }
    return true;
}
```