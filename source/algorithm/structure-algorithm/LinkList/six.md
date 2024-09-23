---
title: 双向链表的封装
date: 2021-02-26 10:57:24
tag: Algorithm
---

### 封装双向链表
```js
function DoublyLinkedList() {
    // 内部类节点
    function Node(data) {
        this.data = data;
        this.prev = null;
        this.next = null;
    }
    // 属性
    this.head = null;
    this.tail = null;
    this.length = 0;

    // 常见的操作
}
```
