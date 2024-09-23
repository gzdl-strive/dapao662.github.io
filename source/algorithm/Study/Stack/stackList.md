---
title: 验证栈序列
date: 2021-03-31 09:55:24
tag: Algorithm
toc: true
---

### 题目描述
>给定 pushed 和 popped 两个序列，每个序列中的 值都不重复，只有当它们可能是在最初空栈上进行的推入 push 和弹出 pop 操作序列的结果时，返回 true；否则，返回 false 。

**示例**
示例 1：
输入：pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
输出：true
解释：我们可以按以下顺序执行：
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
示例 2：
输入：pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
输出：false
解释：1 不能在 2 之前弹出。

**提示：**
0 <= pushed.length == popped.length <= 1000
0 <= pushed[i], popped[i] < 1000
pushed 是 popped 的排列。

### 解题思路
被出栈的元素只有两种可能：即将⼊栈的元素 和 当前栈顶的元素。
思路：贪心算法
1.所有的元素一定是按顺序 push 进去的，重要的是思考怎么 pop 出来的
2.假设当前栈顶元素值为 2，同时对应的 popped 序列中下一个要 pop 的值也为 2，那就必须立刻把这个值 pop
出来。因为之后的 push 都会让栈顶元素变成不同于 2 的其他值，这样再 pop 出来的数 popped
序列就不对应了。
3.将 pushed 队列中的每个数都 push 到栈中，同时检查这个数是不是 popped 序列中下一个要 pop
的值，如果是就把它 pop 出来。
4.最后，检查是不是所有的该 pop 出来的值都 pop 出来了。

```js
/**
 * @param {number[]} pushed
 * @param {number[]} popped
 * @return {boolean}
 */
const validateStackSequences = function (pushed, popped) {
    //使用一个数组模拟栈
    let stack = [];
    //索引j来表示要pop出来的popped数组的位置->从第一个元素开始pop
    let j = 0;
    //遍历pushed数组，每次都push一个数
    for (const key of pushed) {
        //把当前数压入到栈顶
        stack.push(key);
        //判断栈顶元素是否和popped[j]相等
        while (stack.length > 0 && stack[stack.length - 1] === popped[j]) {
            //相等的话，进入循环，把栈顶元素弹出,j后移，接着判断栈顶元素和popped序列的j位置元素是否相等
            stack.pop();
            j++;
        }
    }
    //循环结束后，如果栈为空的话，说明序列正确，如果不为空，说明序列不正确
    return !stack.length;
};
```