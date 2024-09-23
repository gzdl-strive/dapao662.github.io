---
title: 函数的独占时间
date: 2021-04-01 09:16:24
tog: algorithm
toc: true
---

### 题目描述
>给出一个非抢占单线程CPU的 n 个函数运行日志，找到函数的独占时间。每个函数都有一个唯一的 Id，从 0 到 n-1，函数可能会递归调用或者被其他函数调用。函数的独占时间定义是在该方法中花费的时间，调用其他函数花费的时间不算该函数的独占时间。你需要根据函数的 Id 有序地返回每个函数的独占时间。

**示例**
示例 1:
输入:
n = 2
logs = 
["0:start:0",
 "1:start:2",
 "1:end:5",
 "0:end:6"]
输出:[3, 4]
函数 0 在时刻 0 开始，在执行了  2个时间单位结束于时刻 1。
现在函数 0 调用函数 1，函数 1 在时刻 2 开始，执行 4 个时间单位后结束于时刻 5。
函数 0 再次在时刻 6 开始执行，并在时刻 6 结束运行，从而执行了 1 个时间单位。
所以函数 0 总共的执行了 2 +1 =3 个时间单位，函数 1 总共执行了 4 个时间单位。

**说明：**
输入的日志会根据时间戳排序，而不是根据日志Id排序。
你的输出会根据函数Id排序，也就意味着你的输出数组中序号为 0 的元素相当于函数 0 的执行时间。
两个函数不会在同时开始或结束。
函数允许被递归调用，直到运行结束。
1 <= n <= 100

### 解题思路
>可以使用栈来模拟，栈可以用来处理函数的执行和结束
start是入栈任务id,end时出栈任务id
这里需要特别注意的是到底把这个时间段加给谁：当一个新任务启动，我们要把时间段加给上一个任务，当一个任务结束，把时间段加给当前任务

```js
/**
 * @param {number} n
 * @param {string[]} logs
 * @return {number[]}
 */
const exclusiveTime = function (n, logs) {
    let vId = [];//存放id的栈
    let ans = new Array(n).fill(0);
    for (let i = 0, pre = 0; i < logs.length; i++) {
        let pos1 = logs[i].indexOf(':'), pos2 = logs[i].lastIndexOf(':');
        let id = parseInt(logs[i].substr(0, pos1), 10);
        let status = logs[i].substr(pos1 + 1, pos2 - pos1 - 1);
        let time = parseInt(logs[i].substr(pos2 + 1, losg[i].length - pos2), 10);
        if (status === 'start') {
            if (vId.length) {
                ans[vId[vId.length - 1]] += time - pre;
            }
            pre = time;
            vId.push(id);
        } else {
            ans[id] += time - pre + 1;//->给当前id加，当前id就是vId栈顶的元素，所以此时id也可以使用vId[vId.length - 1]来表示 -》ans[vId[vId.length - 1]]
            pre = time + 1;
            vId.pop();
        }
    }
    return ans;
}
```

**代码简化**
>因为在end时候，vId[vId.length - 1]可以表示当前id，那么start和end里面就有同样的代码，可以把它提取出来
```js
/**
 * @param {number} n
 * @param {string[]} logs
 * @return {number[]}
 */
const exclusiveTime = function (n, logs) {
    let vId = [];//存放id的栈
    let ans = new Array(n).fill(0);
    for (let i = 0, pre = 0; i < logs.length; i++) {
        let pos1 = logs[i].indexOf(':'), pos2 = logs[i].lastIndexOf(':');
        let id = parseInt(logs[i].substr(0, pos1), 10);
        let status = logs[i].substr(pos1 + 1, pos2 - pos1 - 1);
        let time = parseInt(logs[i].substr(pos2 + 1, losg[i].length - pos2), 10);
        if (vId.length) ans[vId[vId.length - 1]] += time - pre + (status === 'end');
        pre = time + (status === 'end');
        if (status === 'start')vId.push(id); 
        else vId.pop();
    }
    return ans;
}
```
