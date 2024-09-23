---
title: 删除排序数组中的重复项
date: 2021-02-05 09:24:24
tag: Algorithm
---

### 删除排序数组中的重复项
>题目描述：给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成

**示例:**
给定 nums = [0,0,1,1,1,2,2,3,3,4],
函数应该返回新的长度 5, 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4。
你不需要考虑数组中超出新长度后面的元素。

**说明**
为什么返回数值是整数，但输出的答案是数组呢?
请注意，输入数组是以「引用」方式传递的，这意味着在函数里修改输入数组对于调用者是可见的。
你可以想象内部操作如下:
nums 是以“引用”方式传递的。也就是说，不对实参做任何拷贝
```js
int len = removeDuplicates(nums);
```
在函数里修改输入数组对于调用者是可见的。
根据你的函数返回的长度, 它会打印出数组中该长度范围内的所有元素。
```js
for (int i = 0; i < len; i++) {
    print(nums[i]);
}
```

**解决方法：双指针法**
>数组完成排序后，我们可以放置两个指针i和j, 其中i是慢指针，js是快指针, 只要nums[i] === nums[j]我们就增加 j 以跳过重复的项(这些重复的项后面会被赋值);当nums[i] !== nums[j]时，我们把nums[j]的值复制到nums[i+1].然后递增 i 和 j ,接着我们再次重复相同的过程，直到j到达了数组的末尾，这样i所在的位置就是不重复的数组的位置，而长度为i+1;

```js
/**
 * @{param} {number[]} nums
 * @{param} {number} 
 */
const removeDuplicates = function(nums) {
    if (nums === null || nums.length === ) {
        return ;
    }
    let p = 0, q = 1;//双指针
    // 当快指针还没到达数组的末尾时
    while(q < nums.length) {
        // 当nums[p]等于nums[q]或者nums[p]不等于nums[q],q都会++,所以把q++当成公共的部分
        if (nums[p] !== nums[q]) {
            // 重复的p+1位置会被q的值替换
            // 特殊情况->数组中没有重复的项，所以每次比较时nums[p]都会不等于nums[q],因此每次都会复制一次, 可以加一个小判断，当q - p > 1，才进行复制
            if (q - p > 1) {
                nums[p + 1] = nums[q];
            }
            p++;
        }
        q++;
    }
    return p + 1;
}
const nums = [1, 1, 2];
const len = removeDuplicates(nums);
for (let i = 0; i < len; i++) {
    console.log(nums[i]);//1 2 
}
```


