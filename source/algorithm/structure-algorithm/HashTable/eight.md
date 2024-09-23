---
title: 哈希表的封装
date: 2021-03-01 09:32:24
tag: Algorithm
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

### 封装哈希表类

``` js
function HashTable() {
    // 属性
    this.storage = []; //数组，所有元素都存放到这个数组中
    this.count = 0; //当前哈希表已经存放了多少个元素-》用来计算装载因子(loadFactor) 
    // 装填因子 = 总数据项 / 哈希表长度
    // 随着填装因子变大，效率下降的情况，在不同开放地址法方案中比链地址法更严重
    // loadFactor > 0.75需要进行扩容
    // loadFactor < 0.25需要进行缩减 
    this.limit = 7; //哈希表的总长度

    //哈希函数
    HashTable.prototype.hashFunc = function(str, size) {
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

    // 插入和修改操作
    HashTable.prototype.put = function (key, value) {
        // 1、根据Key获取index
        let index = this.hashFunc(key, this.limit);
        // 2、根据index获取对应的bucket
        let bucket = this.storage[index];
        // 3、判断该bucket是否为空或undefined
        if (bucket === null || bucket === undefined) {
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
            let newSize = this.limit * 2;
            let newPrime = this.getPrime(newSize);
            this.resize(newPrime);
        }
    }

    // 获取操作
    HashTable.prototype.get = function (key) {
        // 1、根据key获取对应的Index
        let index = this.hashFunc(key, this.limit);
        // 2、根据index获取对应的bucket
        let bucket = this.storage[index];
        // 3、判断bucket是否为空或undefined
        if (bucket === null || bucket === undefined) {
            return null;
        }
        // 4、线性查找
        for (let i = 0; i < bucket.length; i++) {
            let tuple = bucket[i];
            if (tuple[0] === key) {
                return tuple[1];
            }
        }
        // 5、没有找到，返回null
        return null;
    }

    // 删除操作
    HashTable.prototype.remove = function (key) {
        // 1、根据Key获取对应index
        let index = this.hashFunc(key, this.limit);
        // 2、根据index获取bucket
        let bucket = this.storage[index];
        // 3、判断bucket是否为空
        if (bucket === null || bucket === undefined) {
            return null;
        }
        // 4、线性查找对应key,并且删除
        for (let i = 0; i < bucket.length; i++) {
            let tuple = bucket[i];
            if (tuple[0] === key) {
                bucket.splice(i, 1);
                this.count -= 1;

                // 缩小容量
                if (this.limit > 7 && (this.count < this.limit * 0.25)) {
                    let newSize = Math.floor(this.limit / 2);
                    let newPrime = this.getPrime(newSize);
                    this.resize(newPrime);
                }

                return tuple[1];
            }
        }
        // 5、没有找到，返回null
        return null;
    }

    // 其他方法
    // 判断哈希表是否为空
    HashTable.prototype.isEmpty = function () {
        return this.count === 0;
    }

    // 获取哈希表中元素的个数
    HashTable.prototype.size = function () {
        return this.count;
    }

    // 哈希表的扩容/缩容
    HashTable.prototype.resize = function (newLimit) {
        // 1、保存旧的数组内容
        let oldStorage = this.storage;
        // 2、重置所有的属性
        this.storage = [];
        this.count = 0;
        this.limit = newLimit;
        // 3、遍历oldStorage种所有的bucket
        for (let i = 0; i < oldStorage.length; i++) {
            // 3.1取出对应的bucket
            let bucket = oldStorage[i];
            // 3.2判断bucket是不是为null或undefined
            if (bucket === null || bucket === undefined) {
                continue;
            }

            // 3.3bucket有数据，那么取出数据，重新插入
            for (let j = 0; j < bucket.length; j++) {
                let tuple = bucket[i];
                this.put(tuple[0], tuple[1]);
            }
        }
    }

    // 判断某个数字是否是质数
    HashTable.prototype.isPrime = function (num) {
        // 1、获取num的平方根
        let sqrt = parseInt(Math.sqrt(num));
        // 2、循环判断
        for (let i = 2; i <= sqrt; i++) {
            if (num % i === 0) {
                return false;
            }
        }
        return true;
    }

    // 获取质数的方法
    HashTable.prototype.getPrime = function (num) {
        // 14 -> 17
        // 34 -> 37
        // 不是质数就递增
        while (!this.isPrime(num)) {
            num++;
        }
        // 直到找到质数返回一个质数为止
        return num;
    }

}
```

### 测试
```js
// 测试
// 1、创建哈希表
let ht = new HashTable();
// 2、插入数据
ht.put('abc', '123');
ht.put('cba', '321');
ht.put('nba', '521');
ht.put('mba', '520');
// 3、获取数据
let get1 = ht.get('abc');
console.log(`获取方法: ${get1}`);//获取方法: 123
// 4、修改方法
ht.put('abc', '111');
get1 = ht.get('abc');
console.log(`修改方法: ${get1}`);//修改方法: 111
// 5、删除方法
ht.remove('abc');
get1 = ht.get('abc');
console.log(`删除方法: ${get1}`);//删除方法: null
let remove = ht.remove('666');
console.log(`删除方法2: ${remove}`)//删除方法2: null
```
