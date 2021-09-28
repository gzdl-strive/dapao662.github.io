---
title: 滑动窗口
date: 2021-09-26 09:57:02
toc: true
---

## 滑动窗口认识
滑动窗口多用于解决一段连续的区间中寻找满足条件的问题，比如说给定一个字符串，寻找无重复字符的最长字串。该思路主要用于数组及字符串的数据结构中。

>滑动窗口主要思路是维护一对指针，在一定条件内右移右指针扩大窗口大小直到窗口内的解不满足题意，此时我们需要根据情况移动左指针，重复移动左右指针的操作并在区间内求解，直到双指针不能再移动

## 大体结构如下
```js
let left = 0, right = 0;
while (right < size) {
  //获取当前索引数据

  right++;
  // 数据更新等操作
  //窗口需要缩小条件
  while () {
    left++;
    // 数据移除等操作
  }
}
```

框架中需要变化的几点如下:
- 右指针右移后数据的更新
- 判断窗口何时需要缩小
- 左指针右移后数据的更新
- 根据题意求解

## 练习
>#LeetCode 03 无重复字符的最长子串
```js
var lengthOfLongestSubstring = function(s) {
  if (!s.length) return 0;
  //left,right用于存放窗口的起始和终止位置，count为当前子串最长长度，map用于存储遍历过的字符
  let left = 0, right = 0, count = 0, map = {};

  //滑动终止条件
  while (right < s.length) {
    const char = s[right];
    //缩小窗口条件：存在重复字符
    if (char in map) {
      //左指针应该移动到重复字符的下一个位置
      //但是，这边可能还存在一种情况，就是如果left指针已经移动过一次了，
      //map里面存储的含有不再当前窗口内的字符，
      //所以我们还需要比较一下重复字符的索引和当前起始位置的索引谁比较大
      left = Math.max(left, map[char] + 1);
    }
    //从上面的代码可知，我们一直维护的是一个不包含重复字符的窗口
    //求解
    count = Math.max(count, right - left + 1);
    //存放字符进map，right++ => 扩大窗口
    map[char] = right++;
  }
  return count;
};
```

>#LeetCode 209 长度最小的子数组
```js
var minSubArrayLen = function(target, nums) {
  //定义一个left,right用于表示滑动窗口起始和结束位置
  //sum用于保存当前窗口内值之和，result存储结果集
  let left = 0, right = 0, sum = 0, result = Infinity;

  while (right < nums.length) {
    //获取当前索引值
    const num = nums[right];
    sum += num;
    //窗口缩小条件-》窗口内的数之和大于target
    //不能使用if,因为可能把左指针位置的数移除后，仍然满足和大于target,需要接着判断
    while (sum >= target) {
      //左指针右移
      //sum需要减去移除的值
      sum -= nums[left];
      //求解-》存到结果集中
      result = Math.min(result, right - left + 1);
      left++;
    }
    //现在我们的窗口内的值之和小于target,所以我们需要扩大窗口
    right++;
  }
  return result === Infinity ? 0 : result;
};
```

>#LeetCode 438. 找到字符串中所有字母异位词
>例如：s="cbaebabacd", p = "abc" --> 输出: [0,6]
```js
var findAnagrams = function(s, p) {
  if (!s.length || !p.length || s.length < p.length) return [];
  //定义left,right指针用来表示滑动窗口起始和终止位置索引
  //map用于存放p中各字符的个数,result存储结果集
  let left = 0, right = 0, map = {}, result = [];

  //把p中各字符对应字符存到map中
  for (let i = 0; i < p.length; i++) {
    const char = p[i];
    map[char] = (char in map) ? map[char] += 1 : 1;
  }

  //滑动终止条件
  while (right < s.length) {
    //获取当前索引的值
    const char = s[right];

    //判断当前值是否在map中，且值要大于0
    if (map[char] > 0) {
      //扩大窗口
      map[char] -= 1;
      right++;
    } else {
      //缩小窗口
      if (map[s[left]] >= 0) {
        //left指针的值存在于map中，那么向右移动时需要还原Map对应值
        map[s[left]] += 1;
        left++;
      } else {
        //left指针对应的字符没有存在map中，那么左指针应该移动到right + 1的位置
        //（为什么是right+1）=> right位置已经不满足条件了
        right += 1;
        left = right;
      }
    }
    //求解
    if (right - left === p.length) {
      result.push(left);
    }
  }
  return result;
};
```

>#LeetCode 76. 最小覆盖子串
```js
var minWindow = function(s, t) {
  //边界条件
  if (!s.length || !t.length || s.length < t.length) return '';
  //定义left,right -> 滑动窗口左右指针，map用于存储t中字符个数
  let left = 0, right = 0, map = {};
  let count = Infinity, start = 0, match = 0;

  //存储t中字符个数
  for (let i = 0; i < t.length; i++) {
    const char = t[i];
    map[char] = (char in map) ? map[char] += 1 : 1;
  }

  //指针移动终止条件
  while (right < s.length) {
    //获取当前索引字符
    const char = s[right];

    //右指针移动时更新数据
    if (char in map) {
      if (map[char] > 0) match += 1;
      map[char] -= 1;
    }

    //窗口缩小条件
    while (match === t.length) {
      //求解
      if (count > right - left + 1) {
        count = right - left + 1;
        start = left;
      }
      //左指针右移-》左指针移动时更新数据
      //还需要判断当前位置字符是否是map里的，如果是，需要还值，match也需要还
      const char = s[left++];
      if (char in map) {
        if (map[char] === 0) match -= 1;//因为t中存在的字符，可能在s中多次出现，所以需要判断一下
        map[char] += 1;
      }
    }

    //右指针移动->扩大窗口
    right++;
  }
  return count === Infinity ? '' : s.substr(start, count);
};
```
