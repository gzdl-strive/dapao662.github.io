---
title: 封装链表类
date: 2022-06-28 10:57:24
toc: true
---

## 节点类型————NodeType
```ts
export type NodeType = string | number;
```

## 链表节点类
```ts
class LinkedNode {
  data: NodeType; // 数据域
  next: LinkedNode | null; // 指针域
  constructor(data: NodeType) {
    this.data = data;
    this.next = null;
  }
}
export default LinkedNode;
```

## 链表类
```ts
import LinkedNode from "./LinkedNode";
import { NodeType } from './types';

// 链表类
class LinkedList {
  head: LinkedNode | null;
  length: number;
  constructor() {
    this.head = null;
    this.length = 0;
  }
  // 1、增——append(element): 向链表的尾部添加一个新的项
  append(element: NodeType) {
    // 创建新节点
    const newNode = new LinkedNode(element);
    // 判断需要添加的是否是第一个节点
    if (this.length === 0) {
      // 如果添加的是第一个节点，说明头节点就是该新节点，直接将head指针指向它即可
      this.head = newNode;
    } else {
      // 不是第一个节点，那么需要先找到最后一个节点，然后将它的指针域指向新节点
      let lastNode: LinkedNode = this.head!;
      // 使用while循环(不知道具体链表长度，所以使用while循环进行向后遍历)
      while (lastNode.next) {
        lastNode = lastNode.next;
      }
      // 找到尾节点了
      lastNode.next = newNode;
    }
    this.length += 1;
  }
  // 2、toString方法
  toString() {
    // 定义变量
    let current: LinkedNode | null = this.head;
    if (!current) return '';
    let listString: string = '';
    // 循环获取一个个节点，然后加入到结果字符串中
    while (current) {
      listString += current.data + ' ';
      current = current.next;
    }
    // 返回结果
    return listString;
  }
  // 3、增——insert(position, element): 向链表的指定位置添加一个新的项
  insert(position: number, element: NodeType) {
    // 对position进行越界判断
    // (1)负数是不合理的
    // (2)插入的位置不能大于链表的长度(可以为length - 1，相当于append)
    if (position < 0 || position > this.length) {
      return false;
    }
    // 创建新节点
    const newNode = new LinkedNode(element);
    // 判断插入的位置是否为第一个
    if (position === 0) {
      // this.head指向第一个节点，把新节点的next指针域指向第一个节点
      newNode.next = this.head;
      // 现在新节点才是头节点，将head指向新节点
      this.head = newNode;
    } else {
      // 不是第一个节点，需要先找到position位置
      let index: number = 0;
      let previous: LinkedNode | null = null;
      let current: LinkedNode | null = this.head!;
      while (index++ < position && current) {
        // 当index小于需要插入的位置，current和previous同时后移
        previous = current;
        current = current.next;
      }
      // 找到需要插入的位置了
      newNode.next = current;
      previous!.next = newNode;
    }
    // 插入成功后，将链表的长度+1，返回true
    this.length += 1;
    return true;
  }
  // 4、查——get(position)
  get(position: number) {
    //判断是否越界
    // 只能获取到length - 1位置的，length上没有数据(和insert插入不同)
    if (position < 0 || position > this.length - 1) {
      return null;
    }
    // 获取对应的数据
    let index: number = 0;
    let current: LinkedNode | null = this.head;
    while (current && index++ < position) {
      current = current.next;
    }
    // 找到对应的位置了，读取数据返回即可
    return current?.data;
  } 
  // 5、查——indexOf(element)
  indexOf(element: NodeType) {
    // 定义变量
    let index: number = 0;
    let current: LinkedNode | null = this.head;
    // 循环查找
    while (current) {
      if (current.data === element) {
        // 找到了返回下标
        return index;
      }
      current = current.next;
      index += 1;
    }
    // 循环结束，说明没有找到，返回-1
    return -1;
  }
  // 6、改——update(position, element)
  update(position: number, element: NodeType) {
    // 判断是否越界
    if (position < 0 || position > this.length - 1) {
      return null;
    }
    // 找到需要修改的位置
    let index: number = 0;
    let current: LinkedNode | null = this.head;
    while (current && index++ < position) {
      current = current.next;
    }
    // 找到对应的位置了->current
    current!.data = element;
    return true;
  }
  // 7、删——removeAt(position): 从列表的特定位置移除一项
  removeAt(position: number) {
    // 越界判断
    if (position < 0 || position > this.length - 1) {
      return null;
    }
    let current: LinkedNode | null = this.head!;
    // 判断删除的是否是第一个节点
    if (position === 0) {
      this.head = this.head!.next;
    } else {
      let index: number = 0;
      let previous: LinkedNode | null = null;
      while (current && index++ < position) {
        previous = current;
        current = current.next;
      }
      // 找到需要删除的节点
      previous!.next = current!.next;
    }
    this.length -= 1;
    return current?.data;
  }
  // 8、删——remove(element): 从链表中移除一项
  remove(element: NodeType) {
    // 获取对应元素的索引
    let position = this.indexOf(element);
    // 如果找到就直接通过索引调用removeAt方法删除
    if (position !== -1) {
      this.removeAt(position);
    }
  }
  // 9、判空——isEmpty()：链表为空返回true，否则返回false
  isEmpty() {
    return this.length === 0;
  }
  // 10、size()：返回链表个数
  size() {
    return this.length;
  }
}

export default LinkedList;
```


## 测试
```ts
import LinkedList from './LinkedList';

// 测试
const list = new LinkedList();

// 测试append方法
list.append('刘备');
console.log(`append: ${list}`);

// 测试insert方法
list.insert(0, '关羽');
list.insert(1, '张飞');
console.log(`insert: ${list}`);

// 测试get方法
console.log(`get: ${list.get(0)}`);
console.log(`get: ${list.get(1)}`);

// 测试indexOf方法
console.log(`indexOf: ${list.indexOf('刘备')}`);
console.log(`indexOf: ${list.indexOf('关羽')}`);
console.log(`indexOf: ${list.indexOf('张飞')}`);
console.log(`indexOf: ${list.indexOf('666')}`);

// 测试update方法
list.update(0, '关羽读春秋');
list.update(1, '张飞——环眼贼、涿郡屠夫');
list.update(2, '刘备——大耳贼');
console.log(`update: ${list}`);

// 测试removeAt方法
list.removeAt(1);
console.log(`removeAt: ${list}`);

// 测试remove方法
list.remove('关羽读春秋');
console.log(`remove: ${list}`);
```