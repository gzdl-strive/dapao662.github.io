---
title: 比较含退格的字符串
date: 2021-03-31 09:47:24
tag: Algorithm
toc: true
---

### 题目描述
>给定 S 和 T 两个字符串，当它们分别被输入到空白的文本编辑器后，判断二者是否相等，并返回结果。 # 代表退格字符。
注意：如果对空文本输入退格字符，文本继续为空。

**示例**
示例 1：
输入：S = "ab#c", T = "ad#c"
输出：true
解释：S 和 T 都会变成 “ac”。
示例 2：
输入：S = "ab##", T = "c#d#"
输出：true
解释：S 和 T 都会变成 “”。
示例 3：
输入：S = "a##c", T = "#a#c"
输出：true
解释：S 和 T 都会变成 “c”。
示例 4：
输入：S = "a#c", T = "b"
输出：false
解释：S 会变成 “c”，但 T 仍然是 “b”。

**提示：**
1 <= S.length <= 200
1 <= T.length <= 200
S 和 T 只含有小写字母以及字符 '#'。

### 解题思路：
1、我们可以利用栈，将两个字符串处理成没有退格的字符串，然后进行比较即可
2、我们创建一个栈，用来处理我们的两个字符串
3、首先处理我们的第一个字符串，我们将字符串的每个元素压入栈。如果遇到#我们就弹出一个元素。当我们遍历完整个字符串的时候，我们就将栈中的字符输出组成字符串。
4、然后我们按相同的步骤处理第二个字符串
5、最后我们比较两个字符串是否相等

```js
/**
 * @param {string} S
 * @param {string} T
 * @return {boolean}
 */
var backspaceCompare = function(S, T) {
    return processed(S) === processed(T);
};
/**
 * @param {string} str 
 * @return {string}
 */
function processed(str) {
    //定义一个栈，这里用数组模拟
    let stack = [];
    //遍历字符串
    for (const key of str) {
        //遇到#弹出栈顶元素
        if (key === '#') {
            stack.pop();
        } else {
            //不是#就压入栈
            stack.push(key);
        }
    }
    //返回一个字符串用于比较->把数组转成字符串
    return stack.join('');
}
```