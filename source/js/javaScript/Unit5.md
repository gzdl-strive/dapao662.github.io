---
title: 第五章
date: 2020-10-26 21:25:10
tag: js
toc: true
---
## 引用类型
>**引用类型的值（对象）是引用类型的一个实例**。
对象是某个特定引用类型的实例。新对象是使用`new`操作符后跟一个构造函数来创建的。
构造函数本身就是一个函数，只不过该函数是出于**创建新对象的目的而定义的**。
### 1、Object类型

### 2、Array类型
* 检测数组：Array.isArray()
* 转换方法：所有对象都具有toLocaleString()、toString()、valueOf()方法
```js
const colors = ["red", "green", "blue"];
console.log(colors.toString());//red,green,blue
console.log(colors.valueOf());//['red', 'green', 'blue']
//join方法可以使用不同的分隔符来构建这个字符串
console.log(colors.join(','));//red,green,blue
console.log(colors.join("||"));//red||green||blue
```

**栈方法**
* 数组--栈（LIFO）--`后进先出push(),pop()`

**队列方法**
* 数组--队列（FIFO）-`先进先出shift(),unshift()`

**重排序方法**
* 数组--`reverse()反转,sort()`

**操作方法**
* `concat`--先创建当前数组的一个副本，然后将接收到的参数添加到这个副本的末尾
* `slice()`接收一个或两个参数，即要返回项的起始和结束位置。1个参数-->返回从该参数指定位置到当前数组的末尾的所有项，2个参数：返回起始和结束直接的项（不包括结束位置的项）
```js
const slice1 = ["red", "green", "blue", "white", "black"];
console.log(slice1.slice(1));//[ 'green', 'blue', 'white', 'black' ]
console.log(slice1.slice(2, 4));//[ 'blue', 'white' ]
//slice参数为负数--》用数组长度加上该数来确定相应的位置
console.log(slice1.slice(-3, -1));//[ 'blue', 'white' ]
```

* `splice()`的主要用途是向数组的中部插入项--返回一个数组-->该数组中包含从原始数组中删除的项
1. 删除：可以删除任意数量的项，只需指定2个参数：要删除的第一项的位置和要删除的项数
```js
const splice1 = ["red", "green", "blue", "white", "black"];
console.log(splice1.splice(0, 2));//[ 'red', 'green' ]
```
2. 插入：可以向指定位置插入任意数量的项，只需提供3个参数：起始位置、0（要删除的项数）、要插入的项
如果要插入多个项，可以再传入第四、第五，以至任意多个项
```js
let splice2 = ["a", "b", "c"];
console.log(splice2.splice(0, 0, "-a"));//[]--返回的是从原始数组中删除的项
console.log(splice2);//[ '-a', 'a', 'b', 'c' ]
```
3. 替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项-->3个参数：起始位置，要删除的项数、要插入的项
```js
let splice3 = ["1", "2", "3"];
console.log(splice3.splice(0, 2, "3-1", "3-2"));//[ '1', '2' ]
console.log(splice3);//[ '3-1', '3-2', '3' ]
```

**位置方法**
`indexOf、lastIndexOf()`:都接收两个参数：要查找的项和（可选的）表示查找起点位置的索引
`indexOf`方法从数组的开头（位置0）开始向后查找，`lastIndexOf`方法从数组的末尾开始向前查找
都返回要查找的项在数组中的位置(在比较第一个参数与数组的每一项时，会使用**全等操作符(===)**)
```js
let number1 = [1, 2, 3, 6, 9, 12];
console.log(number1.indexOf(3));//2
console.log(number1.indexOf(12, 3));//5
```

**迭代方法5个**
每个方法接收两个参数：要在每一项上运行的函数和（可选）运行该函数的作用域对象————影响this的值
传入这些方法中的函数接收**三个参数**：**数组项的值、该项在数组中的位置、数组对象本身**
* `every()`:对数组中的每一项运行给定函数，如果该函数对每一项都返回true,则返回true
* `filter()`:对数组中的每一项运行给定函数，返回该函数会返回true的项组成的数组--过滤
* `forEach()`:对数组中的每一项运行给定函数，这个方法没有返回值。
* `map()`:对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
* `some()`:对数组中的每一项运行给定函数，如果该函数对任一项返回true,则返回true
```js
const number2 = [3, 6, 9, 12, 15, 18, 21];
const every1 = number2.every(function (item, index, array) {
    return (item % 3 === 0)
});
const filter1 = number2.filter(function(item, index, array) {
    return (item % 6 === 0);
});
const forEach1 = number2.forEach(function(item, index, array) {
    return ( item / 3 )
});
const map1 = number2.map(function(item, index, array) {
    return ( item / 3);
});
const some1 = number2.some(function(item, index, array) {
    return ( item % 6 === 0)
});
console.log(every1);//true
console.log(filter1);//[ 6, 12, 18 ]
console.log(forEach1);//undefined
console.log(map1);//[1, 2, 3, 4,5, 6, 7]
console.log(some1);//true
```

**归并方法：reduce()、reduceRight()**
* reduce():从数组的第一项开始，逐个遍历到最后。
* reduceRight():从数组的最后一项开始，向前遍历到第一项.
这两个方法都接受两个参数：一个在每一项上调用的函数和（可选）作为归并基础的初始值。
传给reduce和reduceRight的函数接收**4个参数**：**前一个值、当前值、项的索引、数组对象**
这个函数返回的任何值都会作为第一个参数自动传给下一项
```js
const number3 = [1, 2, 3, 4, 10];
const reduce1 = number3.reduce(function(prev, cur, index, array) {
    return prev + cur;
});
const reduceRight1 = number3.reduceRight(function(prev, cur, index, array) {
    return prev - cur;
})
console.log(reduce1);//20
console.log(reduceRight1);//0
```