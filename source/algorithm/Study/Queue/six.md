---
title: 最近的请求次数
date: 2021-03-21 21:04:24
tag: Algorithm
toc: true
---

### 题目描述
>写一个 RecentCounter 类来计算特定时间范围内最近的请求。
请你实现 RecentCounter 类：
RecentCounter() 初始化计数器，请求数为 0 。
int ping(int t) 在时间 t 添加一个新请求，其中 t 表示以毫秒为单位的某个时间，并返回过去 3000 毫秒内发生的所有请求数（包括新请求）。确切地说，返回在 [t-3000, t] 内发生的请求数。保证 每次对 ping 的调用都使用比之前更大的 t 值。

**提示**：
1 <= t <= 109
保证每次对 ping 调用所使用的 t 值都 严格递增
至多调用 ping 方法 104 次

**解题思路**
>使用队列对过程进行模拟。不断弹出队首的过期元素，然后返回队列大小即可
由于只需要最近的3000以内的数据，每次加入后，然后判断序列最先边的数据是否在这个范围内，如何不在就把它移出，最重要的是，这个输入的时间序列是一个有序的，程序的时间复杂度为:一个平均的时间节点插入数据的个数

```js
const RecentCounter = function () {
    this.pingArray = [];
}
/** 
 * @param {number} t
 * @return {number}
 */
RecentCounter.prototype.ping = function (t) {
    this.pingArray.push(t);
    while (this.pingArray[0] < t - 3000) {
        this.pingArray.shift();
    };
    return this.pingArray.length;
}
```