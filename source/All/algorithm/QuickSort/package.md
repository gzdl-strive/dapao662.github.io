---
title: 封装快速排序
date: 2021-09-08 16:43:02
toc: true
---

## 简单实现
**第一个版本: 枢纽使用最左边元素**
```js
function quick_sort_v1(arr, l, r) {
  if (l >= r) return;
  //使用变量 x y 来指向当前找到的位置
  //base是枢纽值
  let x = l, y = r, base = arr[l];
  //循环终止条件为x >= y
  while (x < y) {
    //如果当前y指针指向的值大于或等于枢纽值，y指针前移
    while (x < y && arr[y] >= base) {
      y--;
    }
    if (x < y) {
      arr[x++] = arr[y];
    }
    while (x < y && arr[x] <= base) {
      x++;
    }
    if (x < y) {
      arr[y--] = arr[x];
    }
  }
  //循环结束，代表x>=y
  arr[x] = base;//arr[y] = base也行
  //分而治之->递归枢纽左右两侧
  quick_sort_v1(arr, l, x - 1);
  quick_sort_v1(arr, x + 1, r);
  return;
}
```

**第二个版本：使用单递归法加无监听**
```js
function quick_sort_v2(arr, l, r) {
  while (l < r) {
    let x = l, y = r, base = arr[l];
    while (x < y) {
      while(x < y && arr[y] >= base) {
        y--;
      }
      if (x < y) arr[x++] = arr[y];
      while (x < y && arr[x] <= base) {
        x++;
      }
      if (x < y) arr[y--] = arr[x];
    }
    arr[y] = base;
    quick_sort_v2(arr, x + 1, r);
    //单边递归方法
    r = x - 1;
  }
  return;
}
```

**第三个版本：使用插入排序优化快速排序**

## 使用构造函数封装快速排序
```js
function ArrayList() {
  //定义属性
  this.data = [];

  //方法
  //1、插入数据
  ArrayList.prototype.insert = function (data) {
    this.data.push(data);
  }
  //2、toString方法
  ArrayList.prototype.toString = function (data) {
    return this.data.join('-');
  }
  //3、交换swap函数
  ArrayList.prototype.swap = function (l, r) {
    [this.data[l], this.data[r]] = [this.data[r], this.data[l]];
  }

    //选择枢纽
  ArrayList.prototype.getMid = function (l, r) {
    //找到中间位置
    let mid = Math.floor((l + r) / 2);
    if (this.data[l] > this.data[mid]) {
      this.swap(l, mid);
    }
    if (this.data[mid] > this.data[r]) {
      this.swap(mid, r);
    }
    if (this.data[l] > this.data[mid]) {
      this.swap(l, mid);
    }
    //排序好的，我们把中间值放到最后面
    this.swap(mid, r);
    //返回枢纽值
    return this.data[r];
  }
  //快速
  ArrayList.prototype.quickSort = function () {
    this.quick(0, this.data.length - 1);
  }
  //快速排序递归方法
  ArrayList.prototype.quick = function (l, r) {
    //边界条件
    if (l >= r) return;
    //获取枢纽
    let pivot = this.getMid(l, r);
    //定义x和y用于记录当前找到的位置
    let x = l, y = r - 1;//最后面的位置是枢纽，所以不用比较
    while (true) {
      while (this.data[x] < pivot) {
        x++;
      }
      while (this.data[y] > pivot) {
        y--;
      }
      if (x < y) {
        this.swap(x, y);
      } else {
        break;
      }
    }
    this.swap(x, r);
    //分而治之
    this.quick(l, x - 1);
    this.quick(x + 1, r);
  }
}
```