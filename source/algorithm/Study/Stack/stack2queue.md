---
title: 化栈为队
date: 2021-03-31 09:36:24
tag: Algorithm
toc: true
---

### 题目描述
>实现一个MyQueue类，该类用两个栈来实现一个队列。

**示例：**
MyQueue queue = new MyQueue();
queue.push(1);
queue.push(2);
queue.peek();  // 返回 1
queue.pop();   // 返回 1
queue.empty(); // 返回 false

**说明：**
你只能使用标准的栈操作 -- 也就是只有 push to top, peek/pop from top, size 和 is empty 操作是合法的。
你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
假设所有操作都是有效的 （例如，一个空的队列不会调用 pop 或者 peek 操作）。

### 解题思路
使用两个栈来实现，一个输入栈，一个输出栈。
输入栈用于读入数据。当需要输出元素时，若输出栈为空。则将输⼊栈的所有元素推送到输出栈，然后取栈顶元素；若输出栈⾮空，则输出栈顶即可。
**输出栈的作⽤是对已经被反转的序列进⾏⼆次反转。**对此感到困惑的同学可以画图模拟⼀下。

```js
//初始化队列->使用两个栈模拟队列,js中使用数组来模拟栈行为
const MyQueue = function () {
    this.stack = [];//输入栈用于输入元素
    this.stackTemp = [];//输出栈用于输出元素
}
/**
 * 入队
 * @param {number} x 
 * @return {void}
 */
MyQueue.prototype.push = function (x) {
    this.stack.push(x);
}
/**
 * 出队
 * @return {number} 
 */
MyQueue.prototype.pop = function () {
    //当stackTemp输出栈为空时，判断输入栈中是否有元素存在，如果存在就压入到输出栈中
    if (!this.stackTemp.length) {
        while (this.stack.length > 0) {
            this.stackTemp.push(this.stack.pop());
        }
    }
    return this.stackTemp.pop();
}
/**
 * 返回队首元素,不改变原队列
 * @return {number} 
 */
MyQueue.prototype.peek = function () {
    //当输出栈为空是，需要把输入栈的元素压入到输出栈中，然后返回队首元素(注意不能改变原队列)
    if (!this.stackTemp.length) {
        while (this.stack.length > 0) {
            this.stackTemp.push(this.stack.pop());
        }
    }
    return this.stackTemp[this.stackTemp.length - 1];
}
/**
 * 判断队列是否为空
 * @return {boolean}
 */
MyQueue.prototype.empty = function () {
    //如果输入栈和输出栈同时为空则队列为空
    return !this.stackTemp.length && !this.stack.length;
}

const queue = new MyQueue();

queue.push(1);
queue.push(2);
console.log(queue.peek());
console.log(queue.pop());
console.log(queue.empty());
```
