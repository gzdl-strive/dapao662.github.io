---
title: 封装队列及其功能的实现
date: 2021-06-19 15:31:24
toc: true
---

## 初步封装队列类
```js
function Queue() {
  //属性
  this.items = [];
  //方法
}
//使用队列
const queue = new Queue();
```

## 队列常见操作
>* enqueue(element): 向队列尾部添加一个(或多个)新的项
>* dequeue(): 移除队列的第一项(即排在队列最前面的项)，并返回被移除的元素
>* front(): 返回队列的第一个元素--最先被添加，也将最先被移除的元素。队列不做任何变动(不移除元素，只返回元素信息)
>* isEmpty(): 如果队列不包含任何元素，返回true,否则返回false
>* size(): 返回队列所包含的元素个数
>* toString(): 将队列中的内容，转换成字符串的形式

## 队列功能的实现
```js
function Queue() {
  //属性
  this.items = [];
  //方法
  //1、enqueue(element):入队-》将元素添加到队列中
  Queue.prototype.enqueue = function(element) {
    this.items.push(element);
  }
  //2、dequeue():出队-》从队列中移除前端元素-》使用数组性能不高，因为删除一个元素，全部都要移动
  Queue.prototype.dequeue = function() {
    this.items.shift();
  }
  //3、查看前端的元素
  Queue.prototype.front = function() {
    return this.items[0];
  }
  //4、判断队列是否为空
  Queue.prototype.isEmpty = function() {
    return this.items.length === 0;
  }
  //5、查看队列中元素的个数
  Queue.prototype.size = function() {
    return this.items.length;
  }
  //6、toString方法
  Queue.prototype.toString = function() {
    return this.items.join(' ');
  }
}
```
**测试**
```js
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

## 队列小游戏
>原游戏规则：
班级中玩一个游戏，所有学生围成一圈，从某位同学手里开始向旁边的同学传递花
这个时候某个人（比如班长），在击鼓，鼓声停下的时候，花在谁手里，谁就出来表演节目
修改游戏规则：
几个朋友围成一圈，开始数数，数到某个数字的人自动淘汰。
最后剩下的这个人获得胜利,请问最后剩下的是原来在哪一个位置上的人？

**使用上面所封装的链表类来实现这个小游戏**
```js
/**
 * {名单队列} nameList
 * {淘汰的数字} num
 * {获胜者下标} return
 */
function passGame(nameList, num) {
  //1、创建一个队列
  const queue = new Queue();
  //2、将名单中的人安装顺序入队
  for (let i = 0; i < nameList.length; i++) {
    queue.enqueue(nameList[i]);
  }
  //3、开始数数，不是Num的人，先出队加入到队尾中，是num的话，直接出队(为什么这么做：队列是受限的数据结构，只能从队首出队，所以需要保证队首是正在数数的那个人)
  //当队列中只有一个人时，循环结束
  while (quque.size() > 1) {
    //3.1、数到num之前的人先出队再入队(即0~num-2,num-1个人出队再入队)
    for (let i = 0; i < num - 1; i++) {
      queue.enqueue(quque.dequque());
    }
    //3.2、数到num的人，直接出队
    queue.dequeue();
  }
  //4、循环结束，队列中只有一个获胜者了
  const champion = queue.front();
  console.log(`获胜者是: ${champion}`);
  //5、返回获胜者的下标
  return nameList.indexOf(champion);
}
```
**测试**
```js
const names = ['关羽','刘备','张飞','赵云','诸葛亮','曹操'];
console.log(passGame(names, 2));
//剩下的人为：诸葛亮
// 4
```
