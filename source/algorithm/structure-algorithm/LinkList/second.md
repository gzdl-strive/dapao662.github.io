---
title: 链表的封装
date: 2021-02-26 10:21:24
tag: Algorithm
---

### 封装链表类
```js
function LinkedList() {
    // 封装内部类：节点类
    function Node(data) {
        this.data = data;
        this.next = null;
    }
    // 属性
    this.head = null;
    this.length = 0;//记录链表长度
}
```