---
title: 实现排序算法
date: 2021-03-10 09:56:24
tag: Algorithm
---

### 封装列表类和排序方法
```js
function ArrayList() {
    // 属性
    this.array = [];
    // 方法
    // 将我们数据插入到数组中  
    ArrayList.prototype.insert = function (data) {
        this.array.push(data);
    }
    // toString方法
    ArrayList.prototype.toString = function () {
        return this.array.join('-');
    }
    //交换两个位置的数据
    Array.prototype.swap = function (m, n) {
        let temp = this.array[m];
        this.array[m] = this.array[n];
        this.array[n] = temp;
    }

    //实现排序算法
    //冒泡排序
    ArrayList.prototype.bubbleSort = function () {
        // 1、获取数组长度
        let len = this.array.length;
        // 第一次i = len - 1,内层循环就比较到len-1
        // 第二次i = len - 2,内层就比较到len - 2
        // ...
        for (let i = len - 1; i >= 0; i--) {
            // 内层循环找最大值，外层循环控制数组长度
            //第一次进来：j=0,比较0和1位置两个数据
            //....
            //最后一次进来：j=len-1，比较len-1和len->所以j<len改为j<len-1(比较len-2和len-1)
            for (let j = 0; j < i; j++) {
                if (this.array[j] > this.array[j + 1]) {
                    this.swap(j, j + 1);
                }
            }
        }
    }

    //选择排序
    ArrayList.prototype.selectionSort = function () {
        // 1、获取数组长度
        const len = this.array.length;
        // 2、外层循环从0位置开始取我们的数据==>i < len - 1(因为到len - 1时只有一个数据了)
        for (let i = 0; i < len - 1; i++) {
            let min = i;
            //内层循环：从min + 1位置开始循环判断
            for (let j = min + 1; j < len; j++) {
                if (this.array[min] > this.array[j]) {
                    min = j;
                }
            }
            this.swap(min, i);
        }
    }
    
    //插入排序
    ArrayList.prototype.insertionSort = function () {
        // 1、获取数组长度
        const len = this.array.length;
        // 2、外层循环：从第一个位置开始获取数据，向前面局部有序进行插入
        for (let i = 1; i < len; i++) {
            // 3、内层循环，获取第i位的数据，和前面的数据依次比较
            let temp = this.array[i];
            let j = i;
            while (this.array[j - 1] > temp && j > 0) {
                this.array[j] = this.array[j-1];
                j--;
            }
            //将j位置的数据，放置temp即可
            this.array[j] = temp;
        }
    }
}
```

### 测试
```js
// 测试
const list = new ArrayList();
//插入元素
list.insert(50);
list.insert(99);
list.insert(10);
list.insert(33);
list.insert(46);
list.insert(26);
list.insert(75);
// toString
console.log(`toString: ${list.toString()}`);//toString: 50-99-10-33-46-26-75 
// 冒泡排序
list.bubbleSort();
console.log(`bubblesort: ${list.toString()}`);//bubblesort: 10-26-33-46-50-75-99
//选择排序
list.selectionSort();
console.log(`selectSort: ${list.toString()}`);//selectSort: 10-26-33-46-50-75-99
//插入排序
list.insertionSort();
console.log(`insertionSort: ${list.toString()}`);//insertionSort: 10-26-33-46-50-75-99
```