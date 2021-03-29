---
title: 设计前中后队列
date: 2021-03-21 19:58:24
tag: Algorithm
---

### 题目描述
>请你设计一个队列，支持在前，中，后三个位置的 push 和 pop 操作
请你完成 FrontMiddleBack 类：
FrontMiddleBack() 初始化队列。
void pushFront(int val) 将 val 添加到队列的 最前面 。
void pushMiddle(int val) 将 val 添加到队列的 正中间 。
void pushBack(int val) 将 val 添加到队里的 最后面 。
int popFront() 将 最前面 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1 。
int popMiddle() 将 正中间 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1 。
int popBack() 将 最后面 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1 。
请注意当有 两个 中间位置的时候，选择靠前面的位置进行操作。比方说：
将 6 添加到 [1, 2, 3, 4, 5] 的中间位置，结果数组为 [1, 2, 6, 3, 4, 5] 。
从 [1, 2, 3, 4, 5, 6] 的中间位置弹出元素，返回 3 ，数组变为 [1, 2, 4, 5, 6] 。

### 解题思路
>为便于后续操作，首先实现一个基于双向链表的双端队列。使用两个双端队列，一个存放前半部分元素，一个存放后半部分元素。通过维护两个队列首尾节点的方式，平衡两个队列的元素数量，使得中间的元素始终处于固定的位置（如维持在第二个链表头部、或第一个链表尾部）。

### 双向链表
```js
const Node = function (val) {
    this.val = val;
    this.next = null;
    this.pre = null;
}
//在当前节点前插入节点
Node.prototype.insert_pre = function (p) {
    //当前节点this
    p.next = this;
    p.pre = this.pre;
    this.pre && (this.pre.next = p);
    this.pre = p;
    return;
}
//在当前节点后插入节点
Node.prototype.insert_next = function (p) {
    p.pre = this;
    p.next = this.next;
    this.next && (this.next.pre = p);
    this.next = p;
    return;
}
//删除当前节点的前一个节点
Node.prototype.delete_pre = function () {
    if (!this.pre) {
        return;
    }
    let p = this.pre;
    this.pre = p.pre;
    if (p.pre) {
        p.pre.next = this;
    }
    return;
}
//删除当前节点的后一个节点
Node.prototype.delete_back = function () {
    if (!this.next) {
        return;
    }
    let p = this.next;
    this.next = p.next;
    if (p.next) {
        p.next.pre = this;
    }
    return;
}
```

### 基于双向链表的双端队列
```js
const deQueue = function () {
    this.cnt = 0;//元素个数
    this.head = new Node(-1);//虚拟头节点
    this.tail = new Node(-1);//虚拟尾节点
    //初始化虚拟头节点和虚拟尾节点
    this.head.next = this.tail;
    this.tail.pre = this.head;
}
//在双端队列结尾插入->插入到虚拟尾节点的前一个
deQueue.prototype.push_back = function (val) {
    this.tail.insert_back(new Node(val));
    this.cnt += 1;
    return;
}
//在双端队列开头插入->插入到虚拟头节点的后一个
deQueue.prototype.push_front = function (val) {
    this.head.insert_next(new Node(val));
    this.cnt += 1;
    return;
}
//在双端队列结尾弹出最后一个元素->弹出虚拟尾节点的前一个结点
deQueue.prototype.pop_back = function () {
    if (this.isEmpty()) {
        return -1;
    }
    let ret = this.tail.pre.val;
    this.tail.delete_pre();
    this.cnt -= 1;
    return ret;
}
////在双端队列开头弹出第一个元素->弹出虚拟头节点的后一个结点
deQueue.prototype.pop_front = function () {
    if (this.isEmpty()) {
        return -1;
    }
    let ret = this.head.next.val;
    this.head.delete_back();
    this.cnt -= 1;
    return ret;
}
//返回第一个元素的值
deQueue.prototype.front = function () {
    return this.head.next.val;
}
//返回最后一个元素的值
deQueue.prototype.back = function () {
    return this.tail.pre.val;
}
//判断是否为空
deQueue.prototype.isEmpty = function () {
    return this.head.next === this.tail;
}
//元素多少
deQueue.prototype.size = function () {
    return this.cnt;
}
```

### 基于双端队列的前中后队列
```js
const FrontMiddleBackQueue = function () {
    //使用两个双端队列来维护
    this.q1 = new deQueue();
    this.q2 = new deQueue();
}
//更新q1,q2两个队列的大小，永远保持q1 = q2或q1 = q2+1
FrontMiddleBackQueue.prototype.update = function () {
    if (this.q1.size() < this.q2.size()) {
        //把q2的第一个插入到q1的最后面去
        this.q1.push_back(this.q2.pop_front());
    }
    if (this.q1.size() === this.q2.size() + 2) {
        this.q2.push_front(this.q1.pop_back());
    }
    return;
}
//判空=>q1的长度永远保持q1 = q2或q1 = q2+1=》所以只要判断q1.size为0即可
FrontMiddleBackQueue.prototype.isEmpty = function () {
    return this.q1.size === 0;
}
//插入元素到最前面->相当于插入到q1队列的最前面
FrontMiddleBackQueue.prototype.pushFront = function (val) {
    this.q1.push_front(val);
    this.update();
    return;
};
//插入到中间
FrontMiddleBackQueue.prototype.pushMiddle = function (val) {
    //例如: q1:[1 2 3] q2:[4 5]  =>插入6 => 应该插入到3的前面 => [1 2 6 3 4 5] => p1: [1 2 6] q2: [3 4 5]
    if (this.q1.size() === this.q2.size() + 1) {
        this.q2.push_front(this.q1.pop_back());
    }
    this.q1.push_back(val);
    this.update();
    return ;
};
//插入到元素的最后面-》相当于插入到q2队列的最后面
FrontMiddleBackQueue.prototype.pushBack = function (val) {
    this.q2.push_back(val);
    this.update();
    return;
};
// 删除最前面的元素，相对于删除q1的头元素
FrontMiddleBackQueue.prototype.popFront = function () {
    if (this.isEmpty()) {
        return -1;
    }
    let ret = this.q1.pop_front();
    this.update();
    return ret;
};
//删除中间的元素
FrontMiddleBackQueue.prototype.popMiddle = function () {
    //如果q1.size() = q2.size() + 1=> 1 2 3 4 5 => 弹出3 => 相当于弹出q1的最后一个
    //如果q1.size() = q2.size() => 1 2 3 4 => 弹出2 => 也是相当于弹出q1的最后一个
    if (this.isEmpty()) {
        return -1;
    }
    let ret = this.q1.pop_back();
    this.update();
    return ret;
};
//删除最后面的元素
FrontMiddleBackQueue.prototype.popBack = function () {
    if (this.isEmpty()) {
        return -1;
    }
    let ret;
    //可能队列只有一个元素，那么P1的长度为1，p2的长度就为0
    if (this.q2.size() === 0) {
        ret = this.q1.pop_back();
    } else {
        ret = this.q2.pop_back();
    }
    this.update();
    return ret;
};
```