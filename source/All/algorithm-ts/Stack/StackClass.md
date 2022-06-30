---
title: 封装栈类
date: 2022-06-30 20:02:24
toc: true
---

>使用数组来模拟

## 栈中元素的类型
```ts
type StackNode = string | number;
```

## 栈类
```ts
import { StackNode } from './types';

// 栈类
class Stack {
  items: StackNode[];
  constructor() {
    this.items = []
  }
  // 入栈push方法
  push(element: StackNode) {
    this.items.push(element);
  }
  // 出栈pop方法
  pop() {
    if (this.isEmpty()) {
      throw new Error('栈为空，无法弹出元素');
    }
    return this.items.pop();
  }
  // 返回栈顶元素
  peek() {
    if (this.isEmpty()) {
      return null;
    }
    return this.items[this.items.length - 1];
  }
  // 判空
  isEmpty() {
    return this.items.length === 0;
  }
  // 长度
  size() {
    return this.items.length;
  }
  // toString
  toString () {
    return this.items.join('——');
  }
}

export default Stack;
```

## 测试
```ts
import Stack from './Stack';

const stack = new Stack();
console.log(`是否为空：${stack.isEmpty()}`);

stack.push('张飞');
stack.push('关羽');
stack.push('刘备');

console.log(`栈顶元素：${stack.peek()}`);
console.log(`栈size：${stack.size()}`);
console.log(`栈元素：${stack.toString()}`);
console.log(`是否为空：${stack.isEmpty()}`);

stack.pop();
stack.pop();
stack.pop();

console.log(`是否为空：${stack.isEmpty()}`);
```

## 练习：使用栈将十进制数字转为二进制
>提示：js的toString方法，也可以进行进制转换`let num = 100; console.log(num.toString(2)); // 1100100`
```ts
import Stack from './Stack';

/**
 * 十进制转二进制：将十进制数字和2整除(二进制是满2进1)，直到结果为0
 */

function dec2bin(decNumber: number) {
  // 复制一份decNumber
  let actionNumber = decNumber;
  // 定义一个栈对象
  const stack = new Stack();
  // 循环，因为不知道具体要循环多少次，所以使用while循环，当被除数不为0时，循环继续
  while (actionNumber > 0) {
    // 将当前数值取余，压入栈中
    stack.push(actionNumber % 2);
    // 需要将当前值除以2，记得要向下取整
    actionNumber = Math.floor(actionNumber / 2);
  }
  // 循环结束，说明栈中已经保存好了我们需要的数据,我们需要逆序去除
  let resultStr = '';
  while (!stack.isEmpty()) {
    resultStr += stack.pop();
  }
  return resultStr;
}

//测试
const result1 = dec2bin(100);
const result2 = dec2bin(50);
const result3 = dec2bin(8);
console.log(result1); // 1100100
console.log(result2); // 110010
console.log(result3); // 1000
```