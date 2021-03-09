---
title: 删除链表的倒数第N个节点
date: 2021-03-08 15:23:24
tag: Algorithm
---

### 题目描述
>给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点

**关键:倒数第n个节点=>找到倒数第n+1个节点=》直接指向倒数第N-1个节点**

### 解题思路
>关键点就是找到倒数第n个节点的前驱节点，然后直接指向倒数第n个节点的后一个节点
>* 如果要找到倒数第n个节点，那么使用快慢指针来，初始时快慢指针都指向head,快指针先运动n步，然后再同时运动，当快指针为null时，慢指针就指向倒数第n个节点（因为快指针先慢指针运动了n步）
>* 但是我们要找到倒数第n+1个节点(即倒数第n个节点的前驱节点)，设置一个虚拟头节点(指向头节点)，让慢指针指向虚拟头节点，快指针指向头节点，然后快指针先运动n次,此时快指针先慢指针(n+1)，然后再同时运动，当快指针为null时，慢指针就指向倒数第n+1个节点

```js
/**
 * @param {ListNode} head 
 * @param {number} n 
 * @return {ListNode}  
 */
const removeNthFromEnd = function (head, n) {
    // 设置虚拟头节点和快慢指针
    let ret = new ListNode(0, head), slow = ret, fast = head;
    //快指针先走n步
    while (n--) {
        fast = fast.next;
    }
    //同时走
    while (fast) {
        fast = fast.next;
        slow = slow.next;
    }
    //慢指针此时就是要删除节点的前驱节点
    slow.next = slow.next.next;
    return ret.next;
}
```

