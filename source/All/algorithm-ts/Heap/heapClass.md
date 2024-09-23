---
title: 堆类
date: 2022-07-20 15:31:24
toc: true
---

## 堆类型
>堆的类型和堆节点类型
```ts
export type HeapNode = number;
export type HeapType = 'greater' | 'less';
```

## 堆类
```ts
import { HeapNode, HeapType } from './types';

class MaxHeap {
  items: HeapNode[]; // 数据
  cnt: number; // 长度
  type: HeapType;
  constructor(type: HeapType = 'greater') {
    this.items = [];
    this.cnt = 0;
    this.type = type;
  }
  // 大小
  size() {
    return this.cnt;
  }
  // 返回堆顶元素
  top() {
    if (!this.size()) return null;
    return this.items[0];
  }
  // 交换元素
  swap(index: number, otherIndex: number) {
    [this.items[index], this.items[otherIndex]] = [this.items[otherIndex], this.items[index]];
  }
  // 向上调整
  shiftUp(index: number) {
    // 0 1 2(父子节点下标关系是2i + 1, 2i + 2)
    // 大顶堆，谁大谁往上
    // 小顶堆，谁小谁往上
    if (this.type === 'greater') {
      while (index && this.items[Math.floor((index - 1) / 2)] < this.items[index]) {
        this.swap(index, Math.floor((index - 1) / 2));
        index = Math.floor((index - 1) / 2);
      }
    } else if (this.type === 'less') {
      while (index && this.items[Math.floor((index - 1) / 2)] > this.items[index]) {
        this.swap(index, Math.floor((index - 1) / 2));
        index = Math.floor((index - 1) / 2);
      }
    }
    return; 
  }
  // 向下调整
  shiftDown(index: number) {
    let n = this.cnt - 1;
    while (index * 2 + 1 <= n) {
      let temp = index; // temp用于指向三元组中最大/最小的数
      if (this.type === 'greater') {
        if (this.items[temp] < this.items[index * 2 + 1]) {
          temp = index * 2 + 1;
        }
        if (index * 2 + 2 <= n && this.items[index * 2 + 2] > this.items[temp]) {
          temp = index * 2 + 2;
        }
      } else if (this.type === 'less') {
        if (this.items[temp] > this.items[index * 2 + 1]) {
          temp = index * 2 + 1;
        }
        if (index * 2 + 2 <= n && this.items[index * 2 + 2] < this.items[temp]) {
          temp = index * 2 + 2;
        }
      }
      // 此时temp就指向了三元组中最大/最小的下标
      // 如果
      if (temp === index) break;
      // 交换temp和index
      this.swap(temp, index);
      index = temp;
    }
    return;
  }
  // 插入
  push(data: HeapNode) {
    // 先将数据插入到尾部，然后进行向上调整
    this.items.push(data);
    this.cnt += 1;
    this.shiftUp(this.cnt - 1);
    this.toString();
  }
  // 弹出
  pop() {
    if (!this.size()) return null;
    let ret;
    if (this.size() === 1) {
      ret = this.items.pop();
      this.cnt = 0;
      return ret;
    }
    // 长度大于1
    ret = this.items[0];
    this.items[0] = this.items.pop()!;
    this.cnt -= 1;
    //向下调整
    this.shiftDown(0);
    this.toString();
    return ret;
  }
  // toString
  toString() {
    console.log(this.items.join(' '));
  }
}

export default MaxHeap;
```

## 测试
```ts
import Heap from './Heap';

const maxHeap = new Heap();

maxHeap.push(5);
maxHeap.push(10);
maxHeap.push(11);
maxHeap.push(6);
maxHeap.push(12);
maxHeap.pop();
maxHeap.pop();
maxHeap.pop();
maxHeap.pop();

const minHeap = new Heap('less');
minHeap.push(5);
minHeap.push(7);
minHeap.push(10);
minHeap.push(2);
minHeap.push(6);
minHeap.push(3);
minHeap.pop();
minHeap.pop();
minHeap.pop();
minHeap.pop();
minHeap.pop();
```
