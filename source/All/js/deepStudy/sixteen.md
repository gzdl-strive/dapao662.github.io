---
title: 数组方法实现
date: 2022-07-30 22:02:02
tag: js
---

## 通用方法
### 1、push
>将一个或多个元素添加到数组的末尾，并返回该数组的新长度
```js
Array.prototype.myPush = function (...items) {
  const array = Object(this);
  const _len = array.length;
  for (let i = 0; i < items.length; i++) {
    array[_len + i] = items[i];
  }
  return array.length;
}
```

### 2、pop
>用于删除数组的最后一个元素
返回值->从数组中删除的元素
```js
Array.prototype.myPop = function () {
  if (!this.length) return null;
  const array = Object(this);
  let _len = array.length;
  const result = array[_len - 1];
  delete array[_len - 1];
  array.length = _len - 1;
  return result;
}
```

### 3、shift
>从数组中删除第一个元素，并返回该元素的值。此方法更改数组的长度
```js
Array.prototype.myShift = function () {
  if (!this.length) return null;
  const array = Object(this);
  const _len = array.length;
  const result = array[0];
  for (let i = 0; i < _len - 1; i++) {
    array[i] = array[i + 1];
  }
  array.length = _len - 1;
  return result;
}
```

### 4、unshift
>将一个或多个元素添加到数组的开头，并返回该数组的新长度，该方法修改原有数组
```js
Array.prototype.myUnshift = function (...items) {
  const array = Object(this);
  const _len = array.length;
  // 先将原来位置的数组后移items.length位
  for (let i = _len + items.length - 1; i >= items.length; i--) {
    array[i] = array[i - items.length];
  }
  // 然后将新元素插入到
  for (let i = 0; i < items.length; i++) {
    array[i] = items[i];
  }
  return array.length;
}
```

## 遍历方法
### 1、map
>返回一个处理过的新数组
>第一个参数：生成新数组元素的函数
>第二个参数：执行callback时的this指向
```js
Array.prototype.myMap = function (callback, thisArg) {
  if (typeof callback !== 'function') {
    throw new TypeError(callback + 'is not a function');
  }
  const newArr = [];
  for (let i = 0; i < this.length; i++) {
    newArr.push(callback.call(thisArg, this[i], i, this));
  }
  return newArr;
}
```

### 2、forEach
>forEach和map类似，唯一区别是forEach是没有返回值的
```js
Array.prototype.myForEach = function (callback, thisArg) {
  if (typeof callback !== 'function') {
    throw new TypeError(callback + 'is not a function');
  }
  for (let i = 0; i < this.length; i++) {
    callback.call(thisArg, this[i], i, this);
  }
}
```

### 3、filter
>过滤，filter使用指定的函数测试所有元素，并创建一个包含所有通过测试的新数组
```js
Array.prototype.myFilter = function (fn) {
  const newArr = [];
  for (let i = 0; i < this.length; i++) {
    if (fn(this[i])) {
      newArr.push(this[i]);
    }
  }
  return newArr;
}
```

### 4、reduce
>reduce方法接收一个函数作为累加器，数组中的每个值(从左到右)开始缩减，最终计算为一个值
```js
Array.prototype.myReduce = function (reducer, initVal) {
  if (!this.length) {
    throw new Error('Reduce of Empty Array');
  };
  let i = 0;
  if (!initVal) {
    i = 1;
    initVal = this[0];
  }
  for (i; i < this.length; i++) {
    initVal = reducer(initVal, this[i], i, this);
  }
  return initVal;
}
```

### 5、find
>find方法返回通过测试(函数内判断)的数组的第一个元素
```js
Array.prototype.myFind = function (fn) {
  if (!this.length) return null;
  for (let i = 0; i < this.length; i++) {
    if (fn(this[i])) return this[i];
  }
  return null;
}
```

### 6、some
>some方法会依次执行数组的每个元素
>如果有一个元素满足条件，返回true
>如果一个都没有满足，返回false
```js
Array.prototype.mySome = function (fn) {
  if (!this.length) return false;
  for (let i = 0; i < this.length; i++) {
    if (fn(this[i])) return true;
  }
  return false;
}
```

### 7、every
>用于检测数组中所有元素是否都符合条件

```js
Array.prototype.myEvery = function (fn) {
  if (!this.length) return false;
  for (let i = 0; i < this.length; i++) {
    if (!fn(this[i])) return false;
  }
  return true;
}
```