---
title: 煎饼排序
date: 2021-03-21 21:52:24
tag: Algorithm
toc: true
---

### 题目描述
>给你一个整数数组 arr ，请使用 煎饼翻转 完成对数组的排序。
一次煎饼翻转的执行过程如下：
选择一个整数 k ，1 <= k <= arr.length
反转子数组 arr[0...k-1]（下标从 0 开始）
例如，arr = [3,2,1,4] ，选择 k = 3 进行一次煎饼翻转，反转子数组 [3,2,1] ，得到 arr = [1,2,3,4] 。
以数组形式返回能使 arr 有序的煎饼翻转操作所对应的 k 值序列。任何将数组排序且翻转次数在 10 * arr.length 范围内的有效答案都将被判断为正确。

示例 1：
输入：[3,2,4,1]
输出：[4,2,4,3]
解释：
我们执行 4 次煎饼翻转，k 值分别为 4，2，4，和 3。
初始状态 arr = [3, 2, 4, 1]
第一次翻转后（k = 4）：arr = [1, 4, 2, 3]
第二次翻转后（k = 2）：arr = [4, 1, 2, 3]
第三次翻转后（k = 4）：arr = [3, 2, 1, 4]
第四次翻转后（k = 3）：arr = [1, 2, 3, 4]，此时已完成排序。 
示例 2：
输入：[1,2,3]
输出：[]
解释：
输入已经排序，因此不需要翻转任何内容。
请注意，其他可能的答案，如 [3，3] ，也将被判断为正确。

**思路**
解题思路：找到最大值位置，反转到头部，然后把整个数组反转，那么最大就会反转到正确的位置，然后找到第二大的值，重复上述操作

```js
//获取数组中最大值的下标
const getMaxIndex = function (arr) {
    let max = 0;
    for (let i = 1; i < arr.length; i++) {
        if (arr[i] > arr[max]) {
            max = i;
        }
    }
    return max;
}
//反转前k个元素
const reverseK = function (a, k) {
    if (k < 1) {
        return;
    }
    let i = 0, j = k;
    while (i < j) {
        [a[i], a[j]] = [a[j], a[i]];
        i++;
        j--;
    }
}

/**
 * @param {number[]} arr
 * @return {number[]}
 */
const pancakeSort = function (arr) {
    let ans = [], max;//ans用来输出反转个数的数组，max是最大值的下标值
    //只有arr的长度大于1才需要反转
    while (arr.length > 1) {
        //例如： 3 2 4 1
        //1、先找到最大值的下标值 -》(最大值4的下标为2)
        max = getMaxIndex(arr);//max = 2
        //2、反转前max + 1个元素=>需要反转3个元素，即最大值下标值 + 1个元素
        max > 0 && ans.push(max + 1);//ans存储的是反转的个数
        reverseK(arr, max);//这边反转前k个元素传入两个参数[需要反转的数组，最后一个的下标值(即最大值的下标值max)]
        reverseK(arr, arr.length - 1);//这次反转整个数组，传入[需要反转的数组，最后一个元素的下标值(即arr.length - 1)]
        ans.push(arr.length);//这次ans传入的是数组长度
        arr.pop();//把最后一个元素弹出
    }
    return ans;
}
```
