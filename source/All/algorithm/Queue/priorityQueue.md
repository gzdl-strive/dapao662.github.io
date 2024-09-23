---
title: 优先队列
date: 2021-06-19 16:06:24
toc: true
---

## 优先级队列特特点
>普通队列插入一个元素，数据会被放在后端。而且需要前面的所以元素都处理完成后才会处理后面的数据
>* 但是优先级队列，在插入一个元素的时候会考虑该数据的优先级
>* 和其他数据优先级进行比较
>* 比较完成后，可以得出这个元素在队列中正确的位置
>* 其他处理方式，和基本队列的处理方式一样

## 优先级队列主要考虑的问题
* 每个元素不再只是一个数据，而且要包含数据的优先级
* 在添加方式中，根据优先级放入正确的位置

## 优先级队列的应用
* 一个现实的例子就是机场登机的顺序--头等舱和商务舱的优先级高于经济舱的乘客
* 另一个现实的例子就是医院的(急诊科)候诊室
* 计算机中，我们也可以通过优先级队列来重新排序队列中任务的顺序--比如每个线程处理的任务重要性不同，我们可以通过优先级的大小，来决定该线程在队列中被处理的次序

## 实现优先级队列相对队列主要有两方面需要考虑：
1、封装元素和优先级放在一起(可以封装一个新的构造函数)
2、添加元素时，将新插入元素的优先级和队列中已经存在的元素进行比较以获得自己正确的位置

## 优先级队列类
```js
function PriorityQueue() {
  //在PriorityQueue重新创建一个类，可以理解成内部类
  function QueueElement(element, priority) {
    this.element = element;
    this.priority = priority;
  }
  //封装属性
  this.items = [];
  //方法
  //1、enqueue(element,priority)方法，入队
  PriorityQueue.prototype.enqueue = function (element, priority) {
    //1、创建QueueElement对象
    const queueElement = new QueueElement(element, priority);
    //2、判断队列是否为空
    if (this.items.length === 0) {
      //2.1队列为空时，不需要判断优先级，直接添加到队列中
      this.items.push(queueElement);
    } else {
      //2.2队列不为空，需要判断优先级
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
  //2、dequeue->从队列中移除前端元素，使用数组性能不高，因为删除一个元素，全部都要移动
  PriorityQueue.prototype.dequeue = function() {
    return this.items.shift();
  }
  //3、front->查看前端元素
  PriorityQueue.prototype.front = function () {
    return this.items[0];
  }
  //4、判断队列是否为空
  PriorityQueue.prototype.isEmpty = function () {
    return this.items.length === 0;
  }
  //5、判断队列中元素的个数
  PriorityQueue.prototype.size = function () {
    return this.items.length;
  }
  //6、toString方法
  PriorityQueue.prototype.toString = function () {
    let resultString = '';
    for (let i = 0; i < this.items.length; i++) {
      resultString += this.items[i].element + '-' + this.items[i].priority + ' ';
    }
    return resultString;
  }
}
```