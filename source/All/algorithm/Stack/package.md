---
title: 封装栈
date: 2021-06-24 16:25:24
toc: true
---

## 栈的常见操作
>1. push(element): 添加一个新元素到栈顶位置
>2. pop(): 移除栈顶的元素，同时返回被移除的元素
>3. peek(): 返回栈顶的元素
>4. isEmpty(): 如果栈里没有任何元素就返回true,否则返回false
>5. size(): 返回栈中的元素个数.
>6. toString(): 将栈结构的内容以字符形式返回

## 封装栈类
```js
function Stack() {
  //栈中的属性
  this.items = [];
  //栈方法
  //入栈
  Stack.prototype.push = function(element) {
    this.items.push(element);
  }
  //出栈
  Stack.prototype.pop = function() {
    return this.items.pop();
  }
  //查看栈顶元素
  Stack.prototype.peek = function() {
    return this.items[this.items.length - 1];
  }
  //判空
  Stack.prototype.isEmpty = function() {
    return this.items.length === 0;
  }
  //栈元素个数
  Stack.prototype.size = function() {
    return this.items.length;
  }
  //toString
  Stack.prototype.toString = function () {
    return this.items.join(' ');
  }
}
```
**测试**
```js
// 栈的使用
const s = new Stack();
console.log(`是否为空: ${s.isEmpty()}`);//是否为空: true
s.push(20);
s.push(10);
s.push(33);
console.log(s.size());//3
console.log('栈顶元素：' + s.peek());//栈顶元素：33
s.pop();
console.log('栈顶元素：' + s.peek());//栈顶元素：10
console.log(s.size());//2
console.log(`是否为空：${s.isEmpty()}`);//是否为空: false
console.log(s.toString());// 20 10
```

## 练习
**十进制转二进制**
要把十进制转化成二进制，我们可以将该十进制数字和2整除(二进制是满二进一)，直到结果是0位置
>例如：
100(十进制) => 二进制
计算： 100 / 2 余数：0
计算： 50 / 2 余数：0
计算： 25 / 2 余数：1
计算： 12 / 2 余数：0
计算： 6 / 2 余数：0
计算： 3 / 2 余数：1
计算： 1 / 2 余数：1
100(十进制) => 1100100（二进制）-》类似栈

**将十进制转成二进制**
```js
function dec2bin(decNumber) {
  //1、定义栈对象
  let stack = new Stack();
  //2、循环/2，并取余数
  while (decNumber > 0) {
    //2.1、获取当前值的余数,压入栈中
    stack.push(decNumber % 2);
    //2.2、把当前值/2
    decNumber = Math.floor(decNumber / 2);
  }
  //3、把栈中的数输出出来
  let resultStr = '';
  while (!stack.isEmpty()) {
    resultStr += stack.pop();
  }
  return resultStr;
}

// 测试
const result1 = dec2bin(100);
const result2 = dec2bin(50);
const result3 = dec2bin(10);
console.log(result1);//1100100
console.log(result2);//110010
console.log(result3);//1010
```