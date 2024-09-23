---
title: 两两交换链表中的节点
date: 2021-03-09 11:03:24
tag: Algorithm
---

### 题目描述
>给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

**示例**
输入：head = [1,2,3,4]
输出：[2,1,4,3]
输入：head = [1]
输出：[1]

### 解题思路一
>其实就是k个一组反转链表的特定情况，把k限定为2就行

### 解题思路二
>迭代反转
```js
const swapPairs = function (head) {
    if (head === null || head.next === null) {
        return head;
    }
    let ret = new ListNode(0, head), p = ret;//创建虚拟头节点
    // p指针指向待反转的两个链表节点的前一个结点
    while (p.next && p.next.next) {
        const node1 = p.next;
        const node2 = p.next.next;
        // p->node1->node2 => p -> node2 -> node1
        p.next = node2;
        node1.next = node2.next;
        node2.next = node1;
        p = node1;
    }
    return ret.next;
}
```