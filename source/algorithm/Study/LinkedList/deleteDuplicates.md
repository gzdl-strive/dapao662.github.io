---
title: 删除排序链表中的重复节点
date: 2021-03-08 15:23:24
tag: Algorithm
---

### 题目描述
>给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

示例 1:
输入: 1->1->2
输出: 1->2
示例 2:
输入: 1->1->2->3->3
输出: 1->2->3

### 解题思路
>直接判断法：创建一个cur指针从head开始遍历，判断当前指针的val是否等于下一个指针的val，如果相等则把当前指针的next指向下一个节点的next(即cur.next.next),如果不相等则cur后移

```js
const deleteDuplicate = function (head) {
    if (head === null || head.next === null) {
        return head;
    }
    //当cur不为空且cur的下一个节点不为空,才可以比较
    let cur = head;
    while (cur && cur.next) {
        if (cur.val === cur.next.val) {
            cur.next = cur.next.next;
        } else {
            // 注意这里不能不放在else中，因为如果出现三个连续重复的元素，不用if else再判断一次cur和cur.next.next是否相等就会出错
            //不加else说明每次都会向后移动，这时错误的
            cur = cur.next;
        }
    }
    return head;
}
```
