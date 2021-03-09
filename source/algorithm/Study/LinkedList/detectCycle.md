---
title: 环形链表II
date: 2021-03-08 10:35:24
tag: Algorithm
---

### 题目描述
>给定一个链表，返回链表开始入环的第一个节点。如果链表无环，则返回 null。

**说明**：不允许修改给定的链表。

**进阶**
你是否可以使用 O(1) 空间解决此题？

### 快慢指针
**思路**：使用快慢指针判断是否是环，如果是环，重新设置一个指针，与慢指针相遇时就是入环第一个节点
**原因**：假设之前慢指针走到入环第一个节点的距离为a,那么快指针已经走了2a（已经在环中了）,那么假设快指针距离慢指针x,那么就是说环的长度是a+x
而快指针一次走两，慢指针一次走一，那么需要x次才能相遇==》也就是慢指针走了x步(从入环的第一个节点走了x步),又环总长为a+x=>那么慢指针只需要再走a步就能回到入环的第一个节点处
那么怎样才能走a步呢，只需要重新设置一个指针ptr，每次走一步，因为慢指针也是走一步，那么等走了a次走，两个节点相遇，即此时ptr节点的位置就是入环的第一个节点

```js
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
const detectCycle = function (head) {
    if (head === null) {
        return null;
    }
    let slow = head, fast = head;
    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow === fast) {
            let ptr = head;
            while (ptr !== slow) {
                slow = slow.next;
                ptr = ptr.next;
            }
            return ptr;
        }
    }
    return null;
}
```