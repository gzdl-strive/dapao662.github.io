---
title: K个一组反转链表
date: 2021-03-08 15:06:24
tag: Algorithm
---

### 题目描述
>给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。
k 是一个正整数，它的值小于或等于链表的长度。
如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

**进阶**
你可以设计一个只使用常数额外空间的算法来解决此问题吗？
你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。

### 解题思路
>创建一个reverseK函数,用来反转链表的前k个节点.创建一个reverse函数，用来判断当前部分链表是否够k个节点,不够的话就返回当前部分链表的head,够的话就调用reverseK函数反转

```js
//reverseK函数：用来反转链表的前k个节点
const reverseK = function (head, k) {
    if (k === 1) {
        return head;
    }
    let tail = head.next, newNode = reverseK(head.next, k - 1);
    head.next = tail.next;
    tail.next = head;
    return newNode;
}
//reverse函数=>判断当前部分链表是否够k个节点
const reverse = function (head, k) {
    let p = head, cnt = k;
    while (--k && p) {
        p = p.next;
    }
    if (p === null) {
        return head;
    }
    return reverseK(head, cnt);
}
// 反转k个节点
const reverseKGroup = function (head, k) {
    let ret = new ListNode(0, head), p = ret, q = p.next;
    // p.next指向反转后的第一个节点，如果发生反转了q就跑到了反转链表的尾部，就不等于p.next
    while ((p.next = reverse(q, k)) !== q) {
        //发生了反转
        p = q;
        q = p.next;
    }
    return ret.next;
}
```

