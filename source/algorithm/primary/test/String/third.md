---
title: 字符串中的第一个唯一字符
date: 2021-3-04 10:58:24
tag: Algorithm
---

### 字符串中的第一个唯一字符
>题目描述：给定一个字符串，找到它的第一个不重复字符，并返回它的索引。如果不存在返回-1

示例：
s = "leetcode"
返回 0
s = "loveleetcode"
返回 2
提示：你可以假定该字符串只包含小写字母

### 解题一：哈希表
**解题思路**
遍历字符串，哈希表统计出现次数。再遍历字符串，找出次数为1的字符

#### (1)Array: Uint16值[0, 65535]过测试用例足够，实际应大一些，下同
```js
const firstUniqChar = function (s) {
    let h = new Unit16Array(26), i = s.length;
    while (i--) {
        h[s.charCodeAt(i) - 97]++;
    }
    i = -1;
    while (++i < s.length) {
        if (h[s.charCodeAt(i) - 97] === 1) {
            return i;
        }
    }
    return -1;
}
const str1 = 'leetcode';
const str2 = 'loveleetcode';
console.log(firstUniqChar(str1));//0
console.log(firstUniqChar(str2));//2
```

#### Object
```js
const firstUniqChar2 = function (s) {
    let h = {}, i = s.length;
    while (i--) {
        h[s[i]] ? h[s[i]]++ : h[s[i]] = 1;
    }
    // console.log(h);//{ e: 3, d: 1, o: 1, c: 1, t: 1, l: 1 }
    i = -1;
    while (++i < s.length) {
        if (h[s[i]] === 1) {
            return i;
        }
    }
    return -1;
}
```

#### Map
```js
const firstUniqChar3 = function (s) {
    let h = new Map();
    let i = s.length;
    while (i--) {
        h.set(s[i], h.has(s[i]) ? h.get(s[i]) + 1 : 1);
    }
    i = -1;
    while (++i < s.length) {
        if (h.get(s[i]) === 1) {
            return i;
        }
    }
    return -1;
}
```

### 解题二：原生
**解题思路**
唯一字符首次出现索引 = 该字符最后出现索引
```js
const firstUniqChar4 = function (s) {
    for (let i = 0; i < s.length; i++) {
        if (s.indexOf(s[i]) === s.lastIndexOf(s[i])) {
            return i;
        }
    }
    return -1;
}
```