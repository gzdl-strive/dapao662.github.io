---
title: 封装堆
date: 2021-08-23 10:00:24
toc: true
---

```js
function Heap(type) {
  //创建属性
  this.data = [];
  this.cnt = 0;
  //方法
  //1、堆大小
  Heap.prototype.size = function () {
    return this.data.length;
  }
  //2、返回堆顶元素，没有返回null
  Heap.prototype.top = function() {
    if (!this.size()) return null;
    return this.data[0];
  }
  //3、插入元素到堆中，需要进行向上调整
  Heap.prototype.push = function (value) {
    this.data.push(value);
    this.cnt += 1;
    this.shift_up(this.cnt - 1);
    this.toString();
  }
  //4、弹出元素，需要进行向下调整
  Heap.prototype.pop = function () {
    if (!this.size()) return null;
    let ret;
    if (this.size() === 1) {
      ret = this.data.pop();
      this.cnt = 0;
      this.toString();
      return ret;
    }
    ret = this.data[0];
    this.data[0] = this.data.pop();
    this.cnt -= 1;
    this.shift_down(0);
    this.toString();
    return ret;
  }
  //5、根据索引交换对应位置元素
  Heap.prototype.swap = function (index, otherIndex) {
    [this.data[index], this.data[otherIndex]] = [this.data[otherIndex], this.data[index]];
  }
  //6、向上调整
  Heap.prototype.shift_up = function (index) {
    if (type === 'greater') {
      while (index && this.data[((index - 1) / 2 | 0)] > this.data[index]) {
        this.swap(index, ((index - 1) / 2 | 0));
        index = ((index - 1) / 2 | 0);
      }
    } else if (type === 'less') {
      while (index && this.data[((index - 1) / 2 | 0)] < this.data[index]) {
        this.swap(index, ((index - 1) / 2 | 0));
        index = ((index - 1) / 2 | 0);
      }
    }
    return;
  }
  //7、向下调整
  Heap.prototype.shift_down = function (index) {
    let n = this.cnt - 1;
    while (index * 2 + 1 <= n) {
      let temp = index;
      if (type === 'greater') {
        if (this.data[index] > this.data[index * 2 + 1]) {
          temp = index * 2 + 1;
        }
        if (index * 2 + 2 <= n && this.data[index * 2 + 2] < this.data[temp]) {
          temp = index * 2 + 2;
        }
      } else if (type === 'less') {
        if (this.data[index] < this.data[index * 2 + 1]) {
          temp = index * 2 + 1;
        }
        if (index * 2 + 2 <= n && this.data[index * 2 + 2] > this.data[temp]) {
          temp = index * 2 + 2;
        }
      }
      if (temp === index) break;
      this.swap(temp, index);
      index = temp;
    }
    return;
  }
  //8、输出函数
  Heap.prototype.toString = function() {
    let result = '';
    for (let key of this.data) {
      result += key + ' ';
    }
    console.log(result);
  }
}
```

**测试**
```js
const minHeap = new Heap('greater');
minHeap.push(5);
minHeap.push(6);
minHeap.push(4);
minHeap.push(10);
minHeap.push(3);
/*
5
5 6
4 6 5
4 6 5 10
3 4 5 10 6
*/
minHeap.pop();
minHeap.pop();
minHeap.pop();
minHeap.pop();
minHeap.pop();
/*
4 6 5 10
5 6 10
6 10
10

*/

const maxHeap = new Heap('less');
maxHeap.push(5);
maxHeap.push(6);
maxHeap.push(4);
maxHeap.push(10);
maxHeap.push(3);
/*
5
6 5
6 5 4
10 6 4 5
10 6 4 5 3
*/

maxHeap.pop();
maxHeap.pop();
maxHeap.pop();
maxHeap.pop();
maxHeap.pop();
/*
6 5 4 3
5 3 4
4 3
3

*/
```