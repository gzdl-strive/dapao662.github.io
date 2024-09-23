---
title: 有效的字母异位词
date: 2021-3-04 11:11:24
tag: Algorithm
---

### 有效的字母异位词
>题目描述：给定两个字符串s和t,编写一个函数来判断t是否是s的字母异位词

**示例**
示例 1:
输入: s = "anagram", t = "nagaram"
输出: true
示例 2:
输入: s = "rat", t = "car"
输出: false

**说明**
你可以假设字符串只包含小写字母。
进阶:
如果输入字符串包含 unicode 字符怎么办？你能否调整你的解法来应对这种情况？

### 解题一:排序
**sort()方法用于对数组的元素进行排序**
```js
const str = 'anagram';
const str2 = 'nagaram';
const str3 = 'rat';
const str4 = 'cat';
const isAnagram = function (s, t) {
    return [...s].sort().join('') === [...t].sort().join('');
}
console.log(isAnagram(str, str2));//true
console.log(isAnagram(str3, str4));//false

/*
console.log([...str]);
console.log([...str].sort());
console.log([...str].join(''));
[
  'a', 'n', 'a',
  'g', 'r', 'a',
  'm'
]
[
  'a', 'a', 'a',
  'g', 'm', 'n',
  'r'
]
anagram
*/
```

### 解法二：哈希表

