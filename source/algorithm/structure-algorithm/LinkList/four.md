---
title: 链表的方法实现
date: 2021-02-26 10:14:24
tag: Algorithm
---

### 链表方法类
```js
function LinkedList() {
    // 内部类：节点类
    function Node(data) {
        this.data = data;
        this.next = null;
    }
    // 属性
    this.head = null;
    this.length = 0;
    // 方法
    // 1、append方法（增）
    LinkedList.prototype.append = function (data) {
        // 1、创建新节点
        const newNode = new Node(data);
        // 2、判断是否添加的是第一个节点
        if (this.length === 0) {
            // 2.1是第一个节点
            this.head = newNode;
        } else {
            // 2.2不是第一个节点
            // 找到最后一个节点
            let current = this.head;
            while (current.next) {
                current = current.next;
            }
            // 最后节点的next指向新的节点
            current.next = newNode;
        }
        // 3、length + 1
        length += 1;
    }

    // 2、toString方法.由于列表项使用了Node类，就需要重写继承自javascript对象默认的toString方法，让其只输出元素的值
    LinkedList.prototype.toString = function () {
        // 1、定义变量
        let current = this.head;
        let listString = '';
        // 2、循环获取一个个节点
        while (current) {
            listString += current.data + ' ';
            current = current.next;
        }
        return listString;
    }

    // 3、insert方法(增)
    // insert(position, element): 项列表的特定位置插入一个新的项
    LinkedList.prototype.insert = function (position, data) {
        // 1、对position进行越界判断
        // (-100)负数不合理
        // position大于长度
        if (position < 0 || position > this.length) {
            return false;
        }
        // 2、根据data创建新节点
        const newNode = new Node(data);
        // 3、判断插入的位置是否是第一个
        if (position === 0) {
            // this.head现在指向第一个节点，把新节点的next指向这个节点
            newNode.next = this.head;
            // 再把this.head指向新节点
            this.head = newNode;
        } else {
            let index = 0;
            let current = this.head;
            let previous = null;
            while (index++ < position) {
                // previous指向current之前的节点
                previous = current;
                current = current.next;
            }
            // 包含插入最后一个位置的情况==>(此时current指向null, previous指向最后一个节点)
            newNode.next = current;
            previous.next = newNode;
        }

        // 4、length + 1
        this.length += 1;
        return true;
    }

    // 4、get方法(查)
    // 获取对应位置的元素
    LinkedList.prototype.get = function (position) {
        // 1、判断是否越界
        // 只能获取到length - 1的位置，length上没有数据
        if (positon < 0 || position >= this.length) {
            return null;
        }
        // 2、获取对应的data
        let current = this.head;
        let index = 0;
        while (index++ < position) {
            current = current.next;
        }

        return current.data;
    }

    // 5、indexOf方法（查）
    // 返回元素在列表中的索引。如果列表中没有改元素则返回-1
    LinkedList.prototype.indexOf = function (data) {
        // 1、定义变量
        let index = 0;
        let current = this.head;
        // 2、开始查找
        while (current) {
            if (current.data === data) {
                return index;
            }
            current = current.next;
            index += 1;
        }
        // 3、找到最后没有找到返回-1
        return -1;
    }

    // 6、update方法 (改)
    // 修改某个位置的元素
    LinkedList.prototype.update = function (position, newData) {
        // 1、判断是否越界
        if (position < 0 || position >= this.length) {
            return false;
        }
        // 2、找到正确的节点
        let index = 0;
        let current = this.head;
        while (index++ < position) {
            current = current.next;
        }

        // 3、将position位置的node改为newData
        current.data = newData;
        return true;
    }

    // 7、removeAt方法(删)
    // removeAt(position): 从列表的特定位置移除一项
    LinkedList.prototype.removeAt = function (position) {
        // 1、越界判断
        if (position < 0 || position >= this.length) {
            return null;
        }
        // 2、判断是否删除的是第一个节点
        let current = this.head;//最后要返回current.data，如果写在里面可能为空
        if (position === 0) {
            this.head = this.head.next;
        } else {
            let index = 0;
            let previous = null;
            while (index++ < position) {
                previous = current;
                current = current.next;
            }
            previous.next = current.next;
        }
        this.length -= 1;
        return current.data;
    }

    // 8、remove方法(删)
    // 从列表中移除一项
    LinkedList.prototype.remove = function (data) {
        // 1、根据data先获取data在列表中的位置
        let position = this.indexOf(data);
        // 2、根据位置信息，删除节点
        return this.removeAt(position);
    }

    // 9、isEmpty方法
    LinkedList.prototype.isEmpty = function () {
        return this.length === 0;
    }

    // 10、size方法
    // 返回链表包含的元素个数
    LinkedList.prototype.size = function () {
        return this.length;
    }
}
```

### 测试
```js
// 1、测试LinedList
const list = new LinkedList();

// 2、测试append方法
list.append('刘备');
list.append('关羽');
list.append('张飞');
console.log(`append: ${list}`);//append: 刘备 关羽 张飞

// 3、测试insert方法
list.insert(0, '曹操');
list.insert(3, '赵云');
list.insert(5, '诸葛亮');
console.log(`insert: ${list}`);//insert: 曹操 刘备 关羽 赵云 张飞 诸葛亮

// 4、测试get方法
console.log(`get1: ${list.get(0)}`);//get1: 曹操
console.log(`get2: ${list.get(5)}`);//get2: 诸葛亮

// 5、测试indexOf方法
console.log(`indexOf1: ${list.indexOf('曹操')}`);//0
console.log(`indexOf2: ${list.indexOf('赵云')}`);//3
console.log(`indexOf3: ${list.indexOf('诸葛亮')}`);//5

// 6、测试update方法
list.update(0, '曹操6');
list.update(3, '赵云6');
list.update(5, '诸葛亮6');
console.log(`update: ${list}`);//update: 曹操6 刘备 关羽 赵云6 张飞 诸葛亮6

// 7、测试removeAt方法
list.removeAt(0);
list.removeAt(3);
console.log(`removeAt(position): ${list}`);//removeAt(position): 刘备 关羽 赵云6 诸葛亮6

// 8、测试remove方法
list.remove('刘备');
list.remove('关羽');
console.log(`remove(element): ${list}`);//remove(element): 赵云6 诸葛亮6

// 9、测试isEmpty
console.log(`isEmpty: ${list.isEmpty()}`);//false

// 10、测试size
console.log(`size: ${list.size()}`);//size: 2
```
