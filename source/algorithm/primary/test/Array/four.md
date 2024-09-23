---
title: 存在重复元素
date: 2021-02-07 15:18:24
tag: Algorithm
---

### 存在重复元素
>题目描述：给定一个整数数组，判断是否存在重复元素。如果存在一值在数组中出现至少两次，函数返回true.如果数组中每个元素都不相同，则返回false.

**示例**
输入: [1,2,3,1]
输出: true
输入: [1,2,3,4]
输出: false

#### 解题一、排序
```js
const containsDuplicate = function(nums) {
    nums.sort((a, b) => a - b);
    const n = nums.length;
    for (let i = 1; i < n; i++) {
        if (nums[i - 1] === nums[i]) {
            return true;
        }
    }
    return false;
}
const arr1 = [1,2,3,4];
const arr2 = [1,2,3,1];
console.log(containsDuplicate(arr1));
console.log(containsDuplicate(arr2));
```
**复杂度分析**
>时间复杂度：O(N log N),其中N为数组的长度。需要对数组进行排序.
>空间复杂度: O(log N)其中N为数组的长度。注意我们在这里应当考虑递归调用栈的深度。

#### 解题二、哈希表
>对于数组中的每个元素，我们将它插入到哈希表中，如果插入一个元素时发现该元素已经存在于哈希表中，则说明存在重复元素

```js
const containsDuplicate = function(nums) {
    const set = new Set();
    for (let key of nums) {
        if (set.has(key)) {
            return true;
        }
        set.add(key);
    }
    return false;
}
const arr3 = [1,2,3,4];
const arr4 = [1,2,3,1];
console.log(containsDuplicate2(arr3));
console.log(containsDuplicate2(arr4));
```