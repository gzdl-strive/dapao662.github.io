---
title: 旋转链表
date: 2021-03-08 15:16:24
tag: Algorithm
---

### 题目描述
>给定一个链表，旋转链表，将链表每个节点向右移动 k 个位置，其中 k 是非负数。

示例 1:
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
示例 2:
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL

### 解题思路
定义一个指针指向链表的尾节点-》让尾节点指向head（形成一个环）->在让指向尾节点的指针向后移动x次(x是链表的长度-k次)
图解:
```js
/*
1->2->3->4->5->NULL, k = 2
    1 -> 2 -> 3 -> 4 -> 5(定义一个tail指针指向这个结点，把tail指向head,形成一个环)
    ^                   |(右移k个位置，相当于tail指针向后移动x次(x是链表长度-k))
    |-------------------|
*/
```

```js
const rotateRight = function (head, k) {
    if (head === null || head.next === null) {
        return head;
    }
    let tail = head, count = 1;
    while (tail && tail.next) {
        tail = tail.next;
        count += 1;
    }
    tail.next = head;//形成一个环
    k %= count;//k可能远大于链表长度
    k = count - k;//tail需要向后移动count-k次
    while (k--) {
        tail = tail.next;
    }
    head = tail.next;//tail.next就是移动后的头节点
    tail.next = null;
    return head;
}
```
