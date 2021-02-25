---
title: 十进制转二进制
date: 2021-02-25 16:45:24
tag: Algorithm
---

### 十进制转二进制分析
计算机里的所有内容都是用二进制表示的，所有十进制转二进制很重要.
**如何实现十进制转二进制？**
要把十进制转化成二进制，我们可以将该十进制数字和2整除(二进制是满二进一)，直到结果是0位置
>例如：
    100(十进制) => 二进制
    计算： 100 / 2  余数：0
    计算： 50 / 2   余数：0
    计算： 25 / 2   余数：1
    计算： 12 / 2   余数：0
    计算： 6 / 2    余数：0
    计算： 3 / 2    余数：1
    计算： 1 / 2    余数：1
    100(十进制) => 1100100（二进制）-》类似栈
### 函数：将十进制转化成二进制

#### 栈类
```js
function Stack() {
    // 栈中的属性
    this.items = [];

    // 栈的相关操作
    //1.将元素压入栈
    Stack.prototype.push = function (element) {
        this.items.push(element);
    }
    //2.从栈中取出元素
    Stack.prototype.pop = function () {
        return this.items.pop();
    }
    //3.查看栈顶元素
    Stack.prototype.peek = function () {
        return this.items[this.items.length - 1];
    }
    //4.判断栈是否为空
    Stack.prototype.isEmpty = function () {
        return this.items.length === 0 
    }
    //5.获取栈中元素的个数
    Stack.prototype.size = function() {
        return this.items.length;
    }
    //6.toString方法
    Stack.prototype.toString = function () {
        return this.items.join(' ');
    }
}
```
#### 函数
```js
function dec2bin(decNumber) {
    // 1、定义栈对象
    const stack = new Stack();

    // 2、循环操作
    while (decNumber > 0) {
        // 2.1获取余数,并放入栈中
        stack.push(decNumber % 2);
        // 2.2获取整除后的结果，作为下一次运行的数字
        decNumber = Math.floor(decNumber / 2);
    }
    // 3、从栈中取出0和1
    let str = '';
    while (!stack.isEmpty()) {
        str += stack.pop();
    }
    return str;
}
// 测试
const result1 = dec2bin(100);
const result2 = dec2bin(50);
const result3 = dec2bin(10);
console.log(result1);//1100100
console.log(result2);//110010
console.log(result3);//1010
```