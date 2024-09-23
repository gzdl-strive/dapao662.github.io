---
title: 反转链表II
date: 2021-03-08 11:17:24
tag: Algorithm
---

### 题目描述
>反转从位置 m 到 n 的链表。请使用一趟扫描完成反转。

**说明**
1 ≤ m ≤ n ≤ 链表长度。

**示例**
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL

### 思路解析
>* 创建一个虚拟头节点，虚拟头节点的next指向head
>* 创建一个p节点赋值位虚拟头节点，再判断要反转的起始位置是否从1开始，如果不是从1开始的，那么p就应该向后移动，直到m-1的位置（p永远指向要反转那部分链表的前一个结点）
>* 创建一个reverseListN函数用来反转前N个节点
>* reverseListN函数反转给定链表的前n个结点->我们要反转的从p.next开始的n-m+1个节点
所以我们只需要把p的next指向反转后的链表即可
最后返回虚拟头节点的next节点(即head节点)

```js
//反转前n个节点的代码-递归
/**
 * @param {ListNode} head 
 * @param {number} n 
 * @return {ListNode}
 */
const reverseListN = function (head, n) {
    if (n === 1) {
        return head;
    }
    let tail = head.next, newNode = reverseListN(head.next, n - 1);
    head.next = tail.next;
    tail.next = head;
    return newNode;
}
//反转区间m-n的链表
/**
 * @param {ListNode} head 
 * @param {number} m 
 * @param {number} n 
 * @param {ListNode} return
 */
const reverseBetween = function (head, m, n) {
    // 创建虚拟头节点
    const ret = new ListNode(0, head), p = ret, cnt = n - m + 1;
    // 循环=》让p永远指向要反转那部分链表的前一个结点
    while (--m) {
        p = p.next;
    }
    // 反转从m开始的节点到n结束(n-m+1个)
    p.next = reverseListN(p.next, cnt);
    return ret.next;
}
```
