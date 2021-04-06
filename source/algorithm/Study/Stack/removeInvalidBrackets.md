---
title: 移除无效的括号
date: 2021-03-31 10:04:24
tag: Algorithm
toc: true
---

### 题目描述
>给你一个由 '('、')' 和小写字母组成的字符串 s。
你需要从字符串中删除最少数目的 '(' 或者 ')' （可以删除任意位置的括号)，使得剩下的「括号字符串」有效。
请返回任意一个合法字符串。
有效「括号字符串」应当符合以下 任意一条 要求：
空字符串或只包含小写字母的字符串
可以被写作 AB（A 连接 B）的字符串，其中 A 和 B 都是有效「括号字符串」
可以被写作 (A) 的字符串，其中 A 是一个有效的「括号字符串」

**示例**
示例 1：
输入：s = "lee(t(c)o)de)"
输出："lee(t(c)o)de"
解释："lee(t(co)de)" , "lee(t(c)ode)" 也是一个可行答案。
示例 2：
输入：s = "a)b(c)d"
输出："ab(c)d"
示例 3：
输入：s = "))(("
输出：""
解释：空字符串也是有效的
示例 4：
输入：s = "(a(b(c)d)"
输出："a(b(c)d)"

**提示**
1 <= s.length <= 10^5
s[i] 可能是 '('、')' 或英文小写字母

### 解题思路：使用栈模拟
使用两个栈用于存放左右括号的索引，当遇到能匹配的上的括号则弹出栈，匹配不上的则删除它

```js
/**
 * @param {string} s
 * @return {string}
 */
const minRemoveToMakeValid = function (s) {
    //创建两个栈用于存放左右括号多余的索引
    let leftDel = [], rightDel = [];
    for (let i = 0; i < s.length; i++) {
        if (s[i] === '(') {
            //遇到左括号，记录下当前的索引
            leftDel.push(i);
        } else if (s[i] === ')') {
            if (leftDel.length) {   
                //遇到右括号且左括好序列不为空，那么弹出与这个右括号可以形成有效的左括号索引(即leftDel的栈顶元素)
                leftDel.pop();
            } else {
                //如果没有与之对应的左括号则把这个右括号加入待删除栈中
                rightDel.push(i);
            }
        }
    }
    //del数组用于合并左右待删除栈中的索引下标
    const res = [...s], del = [...rightDel, ...leftDel];
    //遍历待删除数组中的元素(存放的是一个个待删除的索引)
    for (let i = 0; i < del.length; i++) {
        res[del[i]] = '';//把对应的数组元素至空
    }
    //现在的res中多余的括号已经变成''了,返回这个数组组成的字符串
    return res.join('');
}
```