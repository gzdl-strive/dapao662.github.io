---
title: 反转链表
date: 2021-03-08 10:50:24
tag: Algorithm
---

### 题目描述
>反转一个单链表

**示例**
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL

**进阶**
你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

### 解法一、迭代
反转单链表：迭代解法设置两个指针prev,cur=》初始化prev=null,cur=head ==>我们要的是
cur.next=prev(把当前指针的next指向prev)=>但是当前指针的next指向prev后，cur.next原先连接的就丢失了，不能往下继续运行了，所以我们需要在cur.next指向prev之前保存原来cur.next的指向
所以迭代顺序为1、保存cur.next 2、cur.next指向prev 3、prev后移到cur 4、cur后移到next进行下一次循环
```js
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
const reverseList = function (head) {
    if (head === null || head.next === null) {
        return head;
    }
    let prev = null, cur = head;
    while (cur) {
        // 选择任一写法都行
        //写法一
        const next = cur.next;
        cur.next = prev;
        prev = cur;
        cur = next;
        //写法二:直接解构赋值
        // [cur.next, prev, cur] = [prev, cur, cur.next];
    }
    return prev;
}
```

### 解法二、递归解法
1、为什么可以使用递归
把一个链表拆解成两个部分，第一部分是头，第二部分是除了头剩余的部分=》我们只需要把第二部分反转即可，而第二也可以拆成两部分，头和除了头的剩余部分
。。。以此类推
到最后只剩下一个元素就不要拆解了
(1)大问题拆成两个子问题
(2)子问题求解方式和大问题一样
(3)存在
```js
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
const reverseList = function (head) {
    if (head === null || head.next === null) {
        return head;
    }
    let tail = head.next, newNode = reverseList(head.next);
    head.next = tail.next;
    tail.next = head;
    return newNode;
}
```


