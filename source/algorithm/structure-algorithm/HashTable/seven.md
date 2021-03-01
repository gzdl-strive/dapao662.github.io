---
title: 哈希函数的实现
date: 2021-03-01 10:30:24
tag: Algorithm
---

### 设计哈希函数
>1. 将字符串转成比较大的数字
>2. 将大的数字hashCode压缩到数组范围内(数组大小)

```js
/**
 * 
 * @param {string} str 传入的字符串
 * @param {number} size 数组大小
 */
function hashFunc(str, size) {
    // 1、定义hashCode变量
    let hashCode = 0;
    // 2、霍纳算法，来计算hashCode的值
    //cats -> Unicode编码
    for (let i = 0; i < str.length; i++) {
        // charCodeAt() 方法可返回指定位置的字符的 Unicode 编码
        // Pn(x) = ((...(((anx + a(n-1))x + a(n-2))x + a(n-3))...)x+a1)x + a0
        hashCode = 37 * hashCode + str.charCodeAt(i);
    }

    // 3、取余操作
    let index = hashCode % size;

    return index;
}

// 测试
let res1 = hashFunc('apple', 7);
let res2 = hashFunc('banana', 7);
let res3 = hashFunc('orange', 7);
let res4 = hashFunc('cat', 7);
console.log(res1);//0
console.log(res2);//1
console.log(res3);//4
console.log(res4);//6
```
