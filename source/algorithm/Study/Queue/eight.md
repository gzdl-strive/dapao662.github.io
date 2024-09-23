---
title: 亲密字符串
date: 2021-03-21 21:16:24
tag: Algorithm
toc: true
---

### 题目描述
>给定两个由小写字母构成的字符串 A 和 B ，只要我们可以通过交换 A 中的两个字母得到与 B 相等的结果，就返回 true ；否则返回 false 。交换字母的定义是取两个下标 i 和 j （下标从 0 开始），只要 i!=j 就交换 A[i] 和 A[j] 处的字符。例如，在 "abcd" 中交换下标 0 和下标 2 的元素可以生成 "cbad"

**示例**
示例 1：
输入： A = "ab", B = "ba"
输出： true
解释： 你可以交换 A[0] = 'a' 和 A[1] = 'b' 生成 "ba"，此时 A 和 B 相等。
示例 2：
输入： A = "ab", B = "ab"
输出： false
解释： 你只能交换 A[0] = 'a' 和 A[1] = 'b' 生成 "ba"，此时 A 和 B 不相等。
示例 3:
输入： A = "aa", B = "aa"
输出： true
解释： 你可以交换 A[0] = 'a' 和 A[1] = 'a' 生成 "aa"，此时 A 和 B 相等。
示例 4：
输入： A = "aaaaaaabc", B = "aaaaaaacb"
输出： true
示例 5：
输入： A = "", B = "aa"
输出： false

**提示：**
0 <= A.length <= 20000
0 <= B.length <= 20000
A 和 B 仅由小写字母构成

>两种情况返回true:
>(1)只有两处不同，且两次不同是可交换，如ab,ba
>(2)没有不同，但是所组成的字母有重复

```js
/**
 * 
 * @param {string} A 
 * @param {string} B 
 * @return {boolean}
 */
const buddyStrings = function (A, B) {
    //长度不同肯定不是亲密字符串
    if (A.length !== B.length) {
        return false;
    }
    if (A === B) {
        //判断有没有重复字符串
        return A.length > new Set(A).size;//使用new Set去重
    }
    //剩下判断是否有两次不同
    let i = 0, j;//i和j分别表示第一个不同的位置和第二个不同的位置
    while (A[i] === B[i]) {
        i++;
    }
    //找到第一个不同的位置了
    j = i + 1;//j从i + 1开始查找
    while (j < A.length && A[j] === B[j]) {
        j++;
    }
    //找到第二个位置了(但是也有可能是因为j = A.length结束的循环，需要判断一下)
    if (j === A.length) {
        return false;
    }
    //判断i和j位置元素是否互换了
    if (A[i] !== B[j] || A[j] !== B[i]) {
        return false;
    }
    //执行到这里表示有两个不同位置的元素，现在需要判断这之后的元素是不是都相同
    j += 1;
    while (j < A.length) {
        if (A[j] = B[j]) {
            return false;
        }
        j += 1;
    }
    return true;
}
```