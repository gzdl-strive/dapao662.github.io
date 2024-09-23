---
title: 哈希函数的实现-TypeScript
date: 2022-07-07 15:05:24
tag: Algorithm
---

### 设计哈希函数
>1. 将字符串转成比较大的数字
>2. 将大的数字hashCode压缩到数组范围内(数组大小)

```ts
/**
 * 哈希函数的实现
 * 1、将字符串转成比较大的数字
 * 2、将大的数字压缩到数组范围内(数组大小)=>哈希化
 * @param str 需要转换的字符串
 * @param size 数组大小
 * @returns index
 */
function hashFunc(str: string, size: number): number {
  // 1、定义hashCode变量
  let hashCode: number = 0;

  // 2、通过霍纳法则(秦九韶算法)来计算hashCode值
  for (let i = 0; i < str.length; i++) {
    // 字符的charCodeAt()方法会返回指定位置的字符的UniCode编码
    // Pn(x) = ((...(((anx + a(n-1))x + a(n - 2)x + a(n - 3))...)x + a1)x + a0
    hashCode = hashCode * 37 + str.charCodeAt(i);
  }
  console.log(hashCode);
  // 3、取余操作
  const index: number = hashCode % size;

  return index;
}

// 测试
let res1 = hashFunc('apple', 7); // 0
let res2 = hashFunc('banana', 7); // 1
let res3 = hashFunc('orange', 7); // 4
let res4 = hashFunc('cat', 7); // 6
```
