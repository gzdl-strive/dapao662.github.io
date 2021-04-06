---
title: 有效的括号
date: 2021-03-31 09:58:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。
>有效字符串需满足：
>1、左括号必须用相同类型的右括号闭合。
>2、左括号必须以正确的顺序闭合。

**示例**
示例 1：
输入：s = "()"
输出：true
示例 2：
输入：s = "()[]{}"
输出：true
示例 3：
输入：s = "(]"
输出：false
示例 4：
输入：s = "([)]"
输出：false
示例 5：
输入：s = "{[]}"
输出：true

**提示：**
1 <= s.length <= 104
s 仅由括号 '()[]{}' 组成

### 解题思路：使用栈来实现
1、遍历字符串，当我们遇到一个左括号时，就把它入栈
2、当我们遇到一个右括号时，我们可以取出栈顶元素，判断两个括号是否能闭合。
如果不是相同类型的括号或者栈中没有括号了，那么我们这个字符串就是无效的。
3、遍历结束后，栈如果为空说明是有效，如果不为空，说明不是有效字符串

```js
/**
 * @param {string} s
 * @return {boolean}
 */
var isValid = function (s) {
    //使用数组模拟
    let stack = [];
    //遍历数组
    for (let i = 0; i < s.length; i++) {
        switch (s[i]) {
            //遇到左括号入栈
            case '(':
            case '[':
            case '{': stack.push(s[i]); break;
            //遇到右括号，判断栈是否为空 或 栈顶元素不等于相对应的左括号
            //匹配上的话把对应的左括号出栈
            case ')': if (stack.length === 0 || stack[stack.length - 1] !== '(') return false; stack.pop(); break;
            case ']': if (stack.length === 0 || stack[stack.length - 1] !== '[') return false; stack.pop(); break;
            case '}': if (stack.length === 0 || stack[stack.length - 1] !== '{') return false; stack.pop(); break;
        }
    }
    //栈空说明有效
    return !stack.length;
};
```

**我们可以使用一个map来存储括号**
```js
var isValid2 = function (s) {
    let n = s.length;
    //如果为奇数直接返回false
    if (n % 2 === 1) return false;
    let map = new Map([
        [')', '('],
        [']', '['],
        ['}', '{'],
    ]);
    let stack = [];
    for (const key of s) {
        if (map.has(key)) {
            //右括号
            if (!stack.length || stack[stack.length - 1] !== map.get(key)) return false;
            //匹配成功->把对应左括好弹出
            stack.pop();
        } else {
            //左括号直接入栈
            stack.push(key);
        }
    }
    return !stack.length;
}
```
