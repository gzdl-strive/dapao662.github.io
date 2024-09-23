---
title: 删除排序链表中的重复节点II
date: 2021-03-08 16:19:24
tag: Algorithm
---

### 题目描述
>给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 没有重复出现 的数字。

示例 1:
输入: 1->2->3->3->4->4->5
输出: 1->2->5
示例 2:
输入: 1->1->1->2->3
输出: 2->3

### 解题思路
>创建一个虚拟头节点(因为可能需要删除头节点)
设置两个指针,首先需要一个遍历指针（快指针），遇到相同的节点，需要有跳过相同值节点的操作，
快指针在最基本的只有两个节点值相同的情况下，至少指向第一个相同的节点，此时需要有前继节点（慢指针）的记录，将前继节点指向最后一个相同节点的下一位。
明确了需要记录和操作的节点后，进行正常的遍历逻辑即可。

```js
const deleteDuplicates = function (head) {
    let ret = new ListNode(0, head), p = ret, q;
    while (p.next) {
        if (p.next.next && p.next.val === p.next.next.val) {
            q = p.next.next;
            while (q && q.val === p.next.val) {
                q = q.next;
            }            
            //循环结束：q是不同于p的值的节点
            //删除节点-》p指向需要判断处理的节点的前一个结点
            p.next = q;
        } else {
            p = p.next;
        }
    }
    return ret.next;
}
```
