---
title: 基本计算器II
date: 2021-03-31 10:52:24
tag: Algorithm
toc: true
---

### 题目描述
>给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。整数除法仅保留整数部分。

**示例**
示例 1：
输入：s = "3+2*2"
输出：7
示例 2：
输入：s = " 3/2 "
输出：1
示例 3：
输入：s = " 3+5 / 2 "
输出：5
 
**提示：**
1 <= s.length <= 3 * 105
s 由整数和算符 ('+', '-', '*', '/') 组成，中间由一些空格隔开
s 表示一个 有效表达式
表达式中的所有整数都是非负整数，且在范围 [0, 231 - 1] 内
题目数据保证答案是一个 32-bit 整数


### 解题思路：利用双栈解法，一个用于存储操作数，一个用于存储运算符
遍历字符串，当遇到空格则跳过，遇到操作数则把当前操作数暂时存起来，继续匹配后面的，如果后面的不是操作数了，就把这个数压入操作数栈中，接下来遍历的就是运算符了，如果当前运算符的优先级小于或等于运算符栈栈顶的优先级，则取出操作数栈栈顶(弹出栈并赋值变量)的两个操作数并与运算符栈栈顶的运算符计算，把计算结果压入操作数栈中
压入操作符到运算符栈中
返回操作数栈顶元素（有一个小技巧，在字符串后加一个特殊字符，用于表示表示最低的运算符）

```js
//计算优先级
const level = function (ops) {
    switch(ops) {
        case '+': 
        case '-': return 1;
        case '*': 
        case '/': return 2;
        case '@': return -1;
    }
    return 0;
}
//计算操作数和对应的运算符结果
const calc = function (a, ops, b) {
    switch (ops) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/': return parseInt(a / b);
    }
    return 0;
}
/**
 * @param {string} s 
 * @return {number}
 */
const calculate = function (s) {
    const num = [];//操作数栈
    const ops = [];//运算符栈
    //给s字符串末尾加上一个@操作符，用于表示优先级最低，这样后面就可以让循环里面自己计算，而不用我们在计算一下(后面会判断优先级，优先级于运算符栈顶的优先级就会计算之前的结果)
    s += '@';
    //n用于存储当前操作数的值,如果一个操作数为123=>那么遍历到1时，会存储一个1,当遍历到2时，会计算之前的操作数*10+当前操作数，即1*10+2 = 12 ...
    for (let i = 0, n = 0; i < s.length; i++) {
        //遍历到' '时，跳过
        if (s[i] === ' ') continue;
        //遍历到数字
        if (s[i] >= '0' && s[i] <= '9') {
            n = n * 10 + (s[i] - '0');
            continue;
        }
        num.push(n);
        //n致为0，用于下一次存储
        n = 0;
        //遍历到运算符
        //如果当前运算符栈不为空且当前运算符的优先级小于运算符栈顶的元素的优先级
        while (ops.length && level(s[i]) <= level(ops[ops.length - 1])) {
            //出栈栈顶两个元素，因为栈的特性，先进后出的关系，如果a - b,那么b就要先出栈，然后a出栈
            let b = num.pop();
            let a = num.pop();
            num.push(calc(a, ops[ops.length - 1], b));
            ops.pop();
        }
        ops.push(s[i]);
    }
    return num[num.length - 1];
}
```