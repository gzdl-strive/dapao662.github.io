---
title: 整数反转
date: 2021-3-04 10:43:24
tag: Algorithm
---

### 整数反转
>题目描述：给你一个 32 位的有符号整数 x ，返回 x 中每位上的数字反转后的结果。如果反转后整数超过 32 位的有符号整数的范围 [−2^31,  2^31 − 1] ，就返回 0。（溢出）

示例 1：
输入：x = 123
输出：321
示例 2：
输入：x = -123
输出：-321
示例 3：
输入：x = 120
输出：21
示例 4：
输入：x = 0
输出：0

### 解题一：使用栈-》效率太低（不考虑）

### 解题二：使用数学方法
假设输入x=123
第一次循环结束：n=0+3  x=12
第二次循环节点：n=3*10+2 x=1
第三次循环节点：n=32*10+1 x=0
结束循环，输出n的值

但没有考虑溢出的问题:
>32位符号整数的范围位`[-2^31, 2^31-1]`;溢出条件有两个，一个**大于真实最大值MAX_VALUE**,另一个**小于整数最小值MIN_VALUE**

**思路**
设当前计算结果位ans, 下一位为pop
从`ans * 10 + pop > MAX_VALUE`这个溢出条件来看
* 当出现`ans > MAX_VALUE / 10` 且 还有pop需要添加时，则一定溢出
* 当出现`ans == MAX_VALUE / 10` 且 `pop > 7` 时，则一定溢出，**7是 (2^31 - 1) = 2147483648 - 1 = 2147483647的个位数**

从`ans * 10 + pop < MIN_VALUE`这个溢出条件来看
* 当出现`ans < MIN_VALUE / 10`且还有pop需要添加时，则一定溢出
* 当出现`ans == MIN_VALUE / 10`且 `pop < -8`时，则一定溢出, **8是 (-2^31) = -2147483648的个位数**

### 解法一：官方解法-数学解法
```js
const reverse = function (x) {
    const max = 2**31-1;
    const min = (-2)**31;
    let res = 0;
    while (x !== 0) {
        let pop = x % 10;
        x = parseInt(x / 10);
        if (res > max / 10 || (res === max / 10 && pop > 7)) {
            return 0;
        }
        if (res < min / 10 || (res === min / 10 && pop < -8)) {
            return 0;
        }
        res = res * 10 + pop;
    }
    return res;
}
const num1 = 123;
console.log(reverse(num1));//321
const num2 = 1534236469;
console.log(reverse(num2));//0
```
**复杂度分析**
>时间复杂度：O(log(x))，x中大约有 log10(x)位数字
>空间复杂度：O(1)

### js位运算
**解题思路**
result * 10 + x % 10取出末尾x % 10（负数结果还是负数，无需关心正负），拼接到result中
x / 10去除末位，`| 0 `强制转换位32位有符号整数
通过 `| 0`取整，无论正负，只移除小数点部分(正数向下取整，负数向上取整)
`result | 0 `超过32位的整数转换结果不等于自身，可用作溢出判断

```js
const reverse = function (x) {
    let result = 0;
    while (x !== 0) {
        result = result * 10 + x % 10;
        x = (x / 10) | 0;
    }
    //result | 0 超过32位的整数转换结果不等于自身
    return (result | 0) === result ? result : 0;
}
const num3 = 123;
const num4 = 1534236469;
console.log(reverse2(num3));//321
console.log(reverse2(num4));//0
```
