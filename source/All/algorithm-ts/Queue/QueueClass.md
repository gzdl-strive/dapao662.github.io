---
title: 封装队列类
date: 2022-06-29 10:15:24
toc: true
---

>使用数组来模拟

## 队列中元素的类型
```ts
type QueueValue = string | number;
```

## 队列类
```ts
import { QueueValue } from './types';

class Queue {
  items: QueueValue[];
  constructor() {
    this.items = [];
  }
  // 1、入队——enqueue(element): 将元素添加到队列中
  enqueue(element: QueueValue) {
    // 直接将它添加到末尾即可
    this.items.push(element);
  }
  // 2、出队——dequeue: 从队列中移除前端元素 => 使用数组性能不高，因为删除一个元素，全部都要移动
  dequeue() {
    if (this.isEmpty()) {
      return null;
    }
    return this.items.shift();
  }
  // 3、查询——front: 查看队首元素
  front() {
    if (this.isEmpty()) {
      return null;
    }
    return this.items[0];
  }
  // 4、判空——isEmpty: 判断队列是否为空
  isEmpty() {
    return this.items.length === 0;
  }
  // 5、尺寸——size: 查看队列中元素的个数
  size() {
    return this.items.length;
  }
  // 6、toString
  toString() {
    return this.items.join('——');
  }
}

export default Queue;
```

## 测试
```ts
import Queue from "./Queue";

// 测试队列类
const queue = new Queue;
console.log(`队列是否为空：${queue.isEmpty()}`);// true

queue.enqueue('刘备');
queue.enqueue('关羽');
queue.enqueue('张飞');

console.log(`队列中元素：${queue.toString()}, 队列尺寸大小：${queue.size()}, 查看队列前端元素：${queue.front()}`);
queue.dequeue();
console.log(`队列中元素：${queue.toString()}, 队列尺寸大小：${queue.size()}, 查看队列前端元素：${queue.front()}`);

queue.dequeue();
queue.dequeue();
console.log(`队列中元素：${queue.toString()}, 队列尺寸大小：${queue.size()}, 查看队列前端元素：${queue.front()}`);
```

## 队列小游戏
>规则：几个朋友围成一圈，开始数数，数到某个数字的人自动淘汰。最后剩下的这个人为胜利者，请问最后剩下的的是原来哪一个位置上的人？

```ts
/**
 * @param nameList {名单列表}
 * @param num {淘汰的数字}
 * @return 获胜者下标
 */
const passGame = (nameList: string[], num: number) => {
  // 边界条件，如果不满足条件，不往下进行
  if (!nameList.length || num < 0) {
    return;
  }
  // 通过队列来模拟围成一圈数数，队首的总是正在数数的那个人，
  // 如果它数的不是淘汰数字，那么从队首出队，队尾入队(围成一圈)
  // 如果是淘汰数字，那么直接出队即可

  // 1、创建一个队列
  const queue = new Queue();
  // 2、将名单中的人依次顺序入队
  for (let i = 0; i < nameList.length; i++) {
    queue.enqueue(nameList[i]);
  }
  // 3、设置循环，结束条件为当队列中有且只有一个人时，游戏结束
  while (queue.size() > 1) {
    // 开始数数
    for (let i = 0; i < num - 1; i++) {
      queue.enqueue(queue.dequeue()!);
    }
    // 数到num的需要出队
    queue.dequeue();
  }
  // 4、循环结束，队列中只有一个获胜者了
  const champion = queue.front();
  console.log(`获胜者是：${champion}`);
  // 5、返回获胜者下标
  return nameList.indexOf(champion as string);
}

// 测试
const names: string[] = ['关羽','刘备','张飞','赵云','诸葛亮','曹操'];
console.log(passGame(names, 2));
```