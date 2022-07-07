---
title: 判断质数-TypeScript
date: 2022-07-07 15:12:24
toc: true
---


>质数：只能被1和自己整除的数

### 普通判断质数算法
```ts
function isPrime(num: number): boolean {
  for (let i = 2; i < num; i++) {
    if (num % i === 0) {
      return false;
    }
  }
  return true;
}
let num1 = isPrime(13);
let num2 = isPrime(29);
let num3 = isPrime(123);
console.log('13是否是质数:',num1);//true
console.log('29是否是质数:',num2);//true
console.log('123是否是质数:',num3);//false
```

### 高效判断质数算法
**上面的做法效率并不高，为什么呢？**
对于每个数n,其实并不需要从2判断到n-1;
一个数若可以进行因数分解，那么分解得到的两个数一定是一个`小于等于sqrt(n)`，另一个一定`大于等于sqrt(n)`
比如16可以被分解，那么是2x8，小于sqrt(16)，也就是4.
8大于4.而4x4都是等于sqrt(n)---所以其实我们遍历到等于sqrt(n)即可

```js
function isPrime2(num: number): boolean {
  // 1、获取Num的平方根
  let sqrt = parseInt(Math.sqrt(num) + '');

  // 2、循环判断
  for (let i = 2; i <= sqrt; i++) {
    if (num % i === 0) {
      return false;
    }
  }
  return true;
}

let num4 = isPrime(13);
let num5 = isPrime(29);
let num6 = isPrime(123);
console.log('13是否是质数:',num4);//true
console.log('29是否是质数:',num5);//true
console.log('123是否是质数:',num6);//false
```