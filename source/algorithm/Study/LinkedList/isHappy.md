---
title: 快乐数
date: 2021-03-08 10:37:24
tag: Algorithm
---

### 题目描述
>编写一个算法来判断一个数n是不是快乐数
[快乐数]定义为：
对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。
如果 可以变为  1，那么这个数就是快乐数。
如果 n 是快乐数就返回 true ；不是，则返回 false 。

**示例**
示例 1：
输入：19
输出：true
解释：
1^2 + 9^2 = 82
8^2 + 2^2 = 68
6^2 + 8^2 = 100
1^2 + 0^2 + 0^2 = 1
示例 2：
输入：n = 2
输出：false

**提示**
1 <= n <= 2^31 - 1

### 快慢指针
>路：转换为判断链表是否有环的问题-->如果无限循环说明存在环

```js
/**
 * @param {number} n 
 * @return {boolean} 
 */
const isHappy = function (n) {
    function sum (n) {
        n += '';//转换为字符串，才能使用for..of（如果是number类型会报n is not iterable）
        let sum = 0;
        for (let x of n) {
            sum += x * x;
        }
        return sum;
    }
    let slow = sum(n), fast = sum(slow);
    while (slow !== fast && fast !== 1) {
        slow = sum(slow);
        fast = sum(sum(fast));
    }
    return fast === 1;
}
```

