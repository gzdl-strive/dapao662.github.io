---
title: 环形链表
date: 2021-03-08 10:28:24
tag: Algorithm
---


### 题目描述
>给定一个链表，判断链表中是否有环。

**说明**
如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 
为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 
如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。
如果链表中存在环，则返回 true 。 否则，返回 false

**进阶**
你能用 O(1)（即，常量）内存解决此问题吗？

### 方法一、哈希表存储出现过的节点，如果再次出现说明存在环

```js
const hasCycle = function (head) {
    if (head === null || head.next === null) {
        return false;
    }
    let cur = head;
    const map = new Map();
    while (cur) {
        if (map.has(cur)) {
            return true;
        }
        map.set(cur, true);
        cur = cur.next;
    }
    return false;
}
```


### 方法二、快慢指针
>使用快慢指针 快指针一次向前2个节点 慢指针一次向前1个节点
>* 有环的链表中，快指针和慢指针一定会在环中相遇
>* 无环的链表中，快指针会率先访问到链表尾部 从而终结检测过程

```js
const hasCycle = function (head) {
    if (head === null || head.next === null) {
        return false;
    }
    let slow = head, fast = head;
    while (fast && fast.next) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow === fast) {
            return true;
        }
    }
    return false;
}
```

