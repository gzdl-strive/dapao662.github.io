---
title: 哈希表实现-TypeScript
date: 2022-07-07 15:08:24
toc: true
---

### 我们采用链地址法来实现哈希表

**实现的哈希表(基于storage的数组)每个Index对应的是一个数组(bucket). 当然基于链表也可以**

bucket中存放什么呢? 我们最好将key(类似单词/字符串，比如员工的名字，'Python'...)和value(解释，详情)都放进去，我们继续使用一个数组。(其实其他语言使用元组更好)
最终我们的哈希表的数据格式是这样的： `[[[k,v], [k,v]], [[k,v], [k,v], [k,v]]]`

**代码解析：**
我们定义三个属性：

1. storage作为我们的数组，数组中存放相关的元素
2. count表示当前已经存放了多少数据
3. limit用于标记数组中一共可以存放多少个元素

### 插入和修改数据

**哈希表的插入和修改操作是同一个函数：**
因为使用者传入一个 `<key, value>` 时
(1)如果原来不存在该key，那么就是插入操作
(2)如果已经存在该key, 那么就是修改操作

**代码解析：**
步骤一：根据传入的Key获取对应的index->目的：将我们的数据插入到对应的位置
步骤二：根据索引值取出bucket(桶)->如果桶不存在，创建桶，并且放置在该索引的位置
步骤三：判断新增还是修改原来的值->如果已经有值了，那么就修改值->如果没有，执行后续的添加操作
步骤四：新增操作
步骤五：判断是否需要扩容操作(新增元素之后判断是否是否需要扩容)

### 获取方法

步骤一：根据key获取index
步骤二：根据index获取对应的bucket
步骤三：判断bucket是否为null---->如果为null, 那么直接返回null
步骤四：线性查找bucket中每一个key是否等于传入的Key---->如果等于，那么直接返回对应的value
步骤五：遍历完后，依然没有找到对应的key---->直接返回null

### 删除方法

步骤一：根据key获取对应的index
步骤二：根据Index获取对应的bucket
步骤三：判断bucket是否为null---->如果为null, 那么直接返回null
步骤四：线性查找bucket中每一个key是否等于传入的Key--->如果等于，那么删除 -->删除完之后判断是否需要缩小容量
步骤五：遍历完后，依然没有找到对应的key--->直接返回null

### 哈希表的扩容

**为什么需要扩容？**
(1)目前，我们是将所有的数据项放在长度为7的数组中
(2)因为我们使用的是链地址法，loadFactor可以大于1，所以这个哈希表可以无限制插入新数据
(3)但是，随着数据量的增多，每一个index对应的bucket会越来越长，也就造成效率的降低
(4)所以，在合适的情况对数组进行扩容。比如扩容两倍
**如何进行扩容？**
(1)扩容可以简单的将容量增大两倍（不是质数吗？质数的问题后面再讨论）
(2)但是这种情况下，所有的数据项一定要同时进行修改(重新调用哈希函数，来获取到不同的位置)
(3)比如hashCode=12的数据项，在length=8的时候，Index=4，在长度等于16的时候，Index=12
(4)这是一个耗时的过程，但是如果数组需要扩容，那么这个过程是必要的
**什么情况进行扩容？**
比较常见的情况是loadFactor>0.75的时候进行扩容
比如java的哈希表就是在装填因子大于0.75的时候，对哈希表进行扩容

### 哈希表节点类型
```ts
// 哈希表中键对应的值
export type HashNode = string | number;

// bucket中每个节点，存放一个键值对
type HashNodeArr = [string, HashNode];

// bucket，一个bucket中存放多个节点(使用链地址法，用数组模拟链)
export type HashNodeList = HashNodeArr[];
```

### 封装哈希表类
```ts
import { HashNode, HashNodeList } from './types';

/**
 * 哈希表
 */
class HashTable {
  storage: HashNodeList[]; //数组，所有元素都存放到这个数组中
  count: number; // 当前哈希表中已经存放多少个元素了 => 用来计算装填因子
  // 装填因子 = 总数据项 / 哈希表长度
  // 随着填装因子变大，效率下降的情况，在不同开放地址法方案中比链地址法更严重
  // loadFactor > 0.75需要进行扩容
  // loadFactor < 0.25需要进行缩减 
  limit: number; // 哈希表的总长度

  constructor() {
    this.storage = [];
    this.count = 0;
    this.limit = 7;
  }

  // 哈希函数
  hashFunc(str: string, size: number): number {
    // 1、定义hashCode变量
    let hashCode: number = 0;
    // 2、霍纳法则，来计算hashCode值
    for (let i = 0; i < str.length; i++) {
      hashCode = hashCode * 37 + str.charCodeAt(i);
    }
    // 3、取余操作
    const index: number = hashCode % size;
    return index;
  }

  // 插入和修改操作 -> 插入和修改操作是同一个函数，如果原来不存在该key，就是插入，存在就是修改
  put(key: string, value: HashNode): void {
    // 1、根据key获取index
    const index = this.hashFunc(key, this.limit);
    // 2、根据index获取对应的bucket
    let bucket = this.storage[index];
    // 3、判断该bucket是否为空或undefined
    if (this.isNullUd(bucket)) {
      bucket = [];
      this.storage[index] = bucket;
    }
    // 4、判断是否是修改数据
    for (let i = 0; i < bucket.length; i++) {
      let tuple = bucket[i];
      if (tuple[0] === key) {
        tuple[1] = value;
        return;
      }
    }
    // 5、新增操作
    bucket.push([key, value]);
    this.count += 1;

    // 6、判断是否需要扩容
    if (this.count > this.limit * 0.75) {
      const newSize = this.limit * 2;
      const newPrime = this.getPrime(newSize);
      this.resize(newPrime);
    }
  }

  // 获取操作
  get(key: string): HashNode | null {
    // 1、根据key获取index
    const index: number = this.hashFunc(key, this.limit);
    // 2、获取对应bucket
    const bucket = this.storage[index];
    // 3、判断bucket是否为空或undefined
    if (this.isNullUd(bucket)) {
      return null;
    }
    // 4、线性查找bucket
    for (let i = 0; i < bucket.length; i++) {
      let tuple = bucket[i];
      if (tuple[0] === key) {
        return tuple[1];
      }
    }
    // 5、遍历完bucket没有找到返回null
    return null;
  }

  // 删除操作
  remove(key: string): HashNode | null {
    // 1、根据key获取Index
    const index = this.hashFunc(key, this.limit);
    // 2、根据index获取bucket
    let bucket = this.storage[index];
    // 3、判断bucket是否为空
    if (this.isNullUd(bucket)) {
      return null;
    }
    // 4、线性查找对应key，然后删除
    for (let i = 0; i < bucket.length; i++) {
      let tuple = bucket[i];
      if (tuple[0] === key) {
        bucket.splice(i, 1);
        this.count -= 1;

        // 缩小容量
        // 当进行过扩容，且装填因子小于0.25时才需要缩容
        if (this.limit > 7 && (this.count < this.limit * 0.25)) {
          const newSize = Math.floor(this.limit / 2);
          const newPrime = this.getPrime(newSize);
          this.resize(newPrime);
        }

        return tuple[1];
      }
    }
    // 5、没有找到,返回null
    return null;
  }

  // 判断传入的参数是否为空或undefined
  isNullUd(param: any):boolean {
    return param === null || param === undefined;
  }

  // 判断哈希表是否为空
  isEmpty(): boolean {
    return this.count === 0;
  }
  
  // 获取哈希表中元素的个数
  size(): number {
    return this.count;
  }

  // * 哈希表的扩容/缩容
  resize(newLimit: number): void {
    // 1、保存旧的内容
    const oldStorage = this.storage;
    // 2、重置所有的属性
    this.storage = [];
    this.count = 0;
    this.limit = newLimit;
    // 3、遍历oldStore中的bucket
    for (let i = 0; i < oldStorage.length; i++) {
      // 3.1 取出bucket
      const bucket = oldStorage[i];
      // 3.2 判断bucket是否为Null或undefined
      if (this.isNullUd(bucket)) {
        continue;
      }
      // 3.3 bucket中存在数据，取出数据，重新插入
      for (let j = 0; j < bucket.length; j++) {
        let tuple = bucket[j];
        this.put(tuple[0], tuple[1]);
      }
    }
  }

  // * 判断某个数字是否是质数
  // 质数：一个大于1的自然数，除了1和它本身外，不能被其他自然数整除
  // 对于每个数n,其实并不需要从2判断到n-1;
  // 一个数若可以进行因数分解，那么分解得到的两个数一定是一个小于等于sqrt(n)，另一个一定大于等于sqrt(n)
  // 比如16可以被分解，那么是2x8，小于sqrt(16)，也就是4. 8大于4.而4x4都是等于sqrt(n)，所以其实我们遍历到等于sqrt(n)即可
  isPrime(num: number): boolean {
    // 1、获取num的平方根
    let sqrt = Number(Math.sqrt(num));
    // 2、循环判断
    // i应该从2开始，如果从0开始，就会无限循环下去
    for (let i = 2; i <= sqrt; i++) {
      if (num % i === 0) {
        return false;
      }
    }
    return true;
  }

  // * 获取质数的方法
  getPrime(num: number): number {
    // 14 -> 17
    // 34 -> 37
    // 不是质数就递增
    while (!this.isPrime(num)) {
      num++;
    }
    //直到找到质数返回即可
    return num;
  }
}

export default HashTable;
```

### 测试
```ts
import HashTable from "./HashTable";

const ht = new HashTable();

console.log(`isEmpty: ${ht.isEmpty()}`); // true

// 1、插入数据
ht.put('abc', 123);
ht.put('cba', '666');

console.log(`isEmpty: ${ht.isEmpty()}`); // false

// 2、获取数据
console.log(`get: ${ht.get('abc')}`); // 123
console.log(`get: ${ht.get('666')}`); // null

// 3、修改方法
ht.put('abc', 'new ABC');
console.log(`get: ${ht.get('abc')}`);// new ABC

// 4、删除方法
console.log(`size: ${ht.size()}`);
ht.remove('abc');
console.log(`get: ${ht.get('abc')}`);// null
console.log(`size: ${ht.size()}`);

// 5、验证扩容
ht.put('abc', '111');
ht.put('bcd', '222');
ht.put('cde', '333');
ht.put('def', '444');
ht.put('efg', '555');
console.log('===>', ht.limit); // 17
```