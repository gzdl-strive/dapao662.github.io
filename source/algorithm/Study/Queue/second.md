---
title: 复制带随机指针的链表
date: 2021-03-17 15:55:24
tag: Algorithm
toc: true
---

### 题目描述
>给你一个长度为 n 的链表，每个节点包含一个额外增加的随机指针 random ，该指针可以指向链表中的任何节点或空节点。
构造这个链表的 深拷贝。 深拷贝应该正好由 n 个 全新 节点组成，其中每个新节点的值都设为其对应的原节点的值。新节点的 next 指针和 random 指针也都应指向复制链表中的新节点，并使原链表和复制链表中的这些指针能够表示相同的链表状态。复制链表中的指针都不应指向原链表中的节点 。
例如，如果原链表中有 X 和 Y 两个节点，其中 X.random --> Y 。那么在复制链表中对应的两个节点 x 和 y ，同样有 x.random --> y 。返回复制链表的头节点。
用一个由 n 个节点组成的链表来表示输入/输出中的链表。每个节点用一个 [val, random_index] 表示：
val：一个表示 Node.val 的整数。
random_index：随机指针指向的节点索引（范围从 0 到 n-1）；如果不指向任何节点，则为  null 。
你的代码 只 接受原链表的头节点 head 作为传入参数。

### 思路
1.创建两个指针，一个指向头指针
2.遍历整个链表，复制每个节点，并将值复制成一样的，然后拼接到原节点的后面
3.找到一个克隆节点，然后进行修正random,并将克隆节点的random指向克隆节点
4.拆分链表，分成原节点链表和克隆节点链表
5.返回我们的克隆节点链表

```js
const copyRandomList = function (head) {
    if (head === null) return null;
    let p = head, q, newHead;
    //1、遍历整个链表，复制每个节点，并将值复制成一样的，然后拼接到原节点的后面
    while (p) {
        q = new Node(p.val);//根据p.val创建一个新节点
        q.random = p.random;//新节点的random指向旧节点（新节点复制的节点）的random（后面再把它指向正确的位置）
        //把q插入到p后面
        q.next = p.next;
        p.next = q;
        p = q.next;//p后移到下一个要复制的节点上
    }
    //2、把新复制出来的节点的random指向正确的位置(进行修正random,并将克隆节点的random指向克隆节点)
    p = head.next;
    while (p) {
        // if (p.random) {
        //     p.random = p.random.next;
        // }
        // if (p = p.next) {
        //     p = p.next;
        // }
        //可以简写成
        p.random && (p.random = p.random.next);
        (p = p.next) && (p = p.next);
    }
    //3、拆开链表，分成原节点链表和克隆节点链表
    let p = head;//p从头节点开始，我们要根据p遍历链表
    let newHead = head.next;//newHead为克隆链表头节点
    while (p) {
        q = p.next;
        /*
        A -> A' -> B -> B' -> 
        p    q
        */
       p.next = q.next;
        /*  
        A->B(p.next现在为B)
        */
       if (p.next) {
           q.next = p.next.next;
       }
       /*
       如果p.next不为空,则A'->B'(p.next为B，B的next为B')
       */
       p = p.next;
       /*
       p后移，p现在为B，。。。以此类推
       */
    }
    //4、返回克隆链表的头节点
    return newHead;
}
```
