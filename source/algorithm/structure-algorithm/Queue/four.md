---
title: 优先级队列
date: 2021-02-25 17:20:24
tag: Algorithm
---

### 优先级队列特点
>普通队列插入一个元素，数据会被放在后端。并且需要前面所有的元素都处理完成后才会处理前面的数据
>* 但是优先级队列，在插入一个元素的时候会考虑该数据的优先级
>* 和其他数据优先级进行比较
>* 比较完成后，可以得出这个元素在队列中正确的位置
>* 其他处理方式，和基本队列的处理方式一样

### 优先级队列主要考虑的问题
* 每个元素不再只是一个数据，而且包含数据的优先级
* 在添加方式中，根据优先级放入正确的位置

### 优先级队列的应用
* 一个现实的例子就是机场登机的顺序
-----头等舱和商务舱的优先级高于经济舱的乘客
* 另一个现实的例子是医院的(急诊科)候诊室
计算机中，我们也可以通过优先级队列来重新排序队列中任务的顺序
-----比如每个线程处理的任务重要性不同，我们可以通过优先级的大小，来决定该线程在队列中被处理的次序

**实现优先级队列相对队列主要有两方面需要考虑：**
1. 封装元素和优先级放在一起(可以封装一个新的构造函数)
2. 添加元素时，将新插入元素的优先级和队列中已经存在的元素进行比较以获得自己正确的位置.

### 优先级队列类
```js
function PriorityQueue() {
    // 在PriorityQueue重新创建了一个类，可以理解成内部类
    function QuqueElement(element, priority) {
        this.element = element;
        this.priority = priority;
    }
    // 封装属性
    this.items = [];
    // 实现插入方法
    PriorityQueue.prototype.enqueue = function (element, priority) {
        // 1、创建QuqueElement对象
        const queueElement = new QuqueElement(element, priority);
        // 2、判断队列是否为空
        if (this.items.length === 0) {
            this.items.push(queueElement);
        } else {
            let added = false;
            for (let i = 0; i < this.items.length; i++) {
                if (queueElement.priority < this.items[i].priority) {
                    this.items.splice(i, 0, queueElement);
                    added = true;
                    break;
                }
            }
            if (!added) {
                this.items.push(queueElement);
            }
        }
    }
    // 2、从队列中移除前端元素-->使用数组性能不高，因为删除一个元素，全部都要移动
    PriorityQueue.prototype.dequeue = function () {
        return this.items.shift();
    }
    // 3、查看前端的元素
    PriorityQueue.prototype.front = function () {
        return this.items[0];
    }
    // 4、查看队列是否为空
    PriorityQueue.prototype.isEmpty = function () {
        return this.items.length === 0;
    }
    // 5、查看队列中元素的个数
    PriorityQueue.prototype.size = function () {
        return this.items.length;
    }
    // 6、toString方法
    PriorityQueue.prototype.toString = function () {
        let resultString = '';
        for (let i = 0; i < this.items.length; i++) {
            resultString += this.items[i].element + '-' + this.items[i].priority + ' ';
        }
        return resultString;
    }
}
// 测试代码
const pq = new PriorityQueue();
// enqueue方法
pq.enqueue('abc', 111);
pq.enqueue('nba', 20);
pq.enqueue('mba', 1000);
pq.enqueue('rush', 555);
console.log(pq.toString());
```
