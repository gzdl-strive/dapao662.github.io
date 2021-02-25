---
title: 队列常见操作
date: 2021-02-25 17:15:24
tag: Algorithm
---

### 队列常见操作
>* enqueue(element): 向队列尾部添加一个(或多个)新的项
>* dequeue(): 移除队列的第一(即排在队列最前面的)项，并返回被移除的元素
>* front(): 返回队列中的第一个元素——最先被添加，也将是最先被移除的元素。队列不做任何变动(不移除元素，只返回元素信息——与stack的peek方法非常类似)
>* isEmpty(): 如果队列中不包含任何元素，返回true,否则返回false.
>* size(): 返回队列包含的元素个数，与数组的length属性类似
>* toString(): 将队列中的内容，转换成字符串形式

### 队列类
```js
function Queue() {
    // 属性
    this.items = [];
    // 方法
    // 1、将元素添加到队列中
    Queue.prototype.enqueue = function (element) {
        this.items.push(element);
    }
    // 2、从队列中移除前端元素->使用数组性能不高，因为删除一个元素，全部都要移动
    Queue.prototype.dequeue = function () {
        return this.items.shift();
    }
    // 3、查看前端的元素
    Queue.prototype.front = function () {
        return this.items[0];
    }
    // 4、查看队列是否为空
    Queue.prototype.isEmpty = function () {
        return this.items.length === 0;
    }
    // 5、查看队列中元素的个数
    Queue.prototype.size = function () {
        return this.items.length;
    }
    // 6、toString方法
    Queue.prototype.toString = function () {
        return this.items.join(' ');
    }
}
//使用队列
const queue = new Queue();
console.log(`队列是否为空: ${queue.isEmpty()}`);//队列是否为空: true
queue.enqueue('abc');
queue.enqueue('def');
queue.enqueue('gif');
console.log(`队列前端元素：${queue.front()}`);//队列前端元素：abc
console.log(`队列尺寸：${queue.size()}`);//队列尺寸：3
console.log(queue.toString());//abc def gif
queue.dequeue();
console.log(`队列前端元素：${queue.front()}`);//队列前端元素：def
console.log(`队列尺寸：${queue.size()}`);//队列尺寸：2
console.log(queue.toString());//def gif
```