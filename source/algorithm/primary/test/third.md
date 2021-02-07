---
title: 旋转数组
date: 2021-02-07 15:06:24
tag: Algorithm
---

### 旋转数组
>题目描述：给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。你可以使用空间复杂度为 O(1) 的 原地 算法解决这个问题吗？

**示例**
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]

#### 解题一、使用额外的数组**
我们可以使用额外的数组来将每个元素存放到正确的位置。
用n表示数组长度，我们遍历原数组，将原数组下标为i的元素放至新数组下标为(i + k) mod n的位置，最后将新数组拷贝给原数组即可。

```js
const rotate = function(nums, k) {
    const n = nums.length;
    const newArr = new Array(n);
    for (let i = 0; i < n; i++) {
        newArr[(i + k) % n] = nums[i];
    }
    for (let i = 0; i < n; i++) {
        nums[i] = newArr[i];
    }
    return nums;
}
const arr1 = [1,2,3,4,5,6,7];
console.log(rotate(arr1, 3));//[5, 6, 7, 1, 2, 3, 4]
```
**复杂度分析**
>时间复杂度O(n),其中n为数组长度
>空间复杂度O(n)

#### 解题二、数组翻转(推荐)
>该方法基于如下的事实：当我们将数组的元素向右移动k次后，尾部k mod n 个元素会移动至数组头部，其余元素向后移动k mod n个位置
>该方法为数组的翻转：我们可以先将所有元素翻转，这样尾部的k mod n 个元素就被移动至数组头部，然后我们再翻转[0, k mod n - 1]区间的元素和[k mod n, n-1]区间的元素即能得到最后的答案。
我们以n = 7, k = 3为例
操作                            结果
原始数组                        1234567
翻转所有元素                    7654321
翻转[0, k mod n - 1]区间元素    5674321
赋值[k mod n, n - 1]区间元素    5671234

```js
const reverse = (nums, start, end) => {
    while (start < end) {
        let temp = nums[start];
        nums[start] = nums[end];
        nums[end] = temp;
        start ++;
        end --;
    }
}
const rotate = function(nums, k) => {
    k %= nums.length;
    reverse(nums, 0, nums.length - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, nums.length - 1);
    return nums;
}
const arr2 = [1,2,3,4,5,6,7];
console.log(rotate(arr2, 3));//[5, 6, 7, 1, 2, 3, 4]
```
**复杂度分析**
>时间复杂度: O(n),n为数组的长度。每个元素被翻转两次，一共n个元素，因此总时间复杂度为O(2n) = O(n)
>空间复杂度：O(1)

