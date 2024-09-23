---
title: 双向链表操作的实现
date: 2021-02-26 10:14:24
tag: Algorithm
---

## 双向链表类
```js
function DoublyLinkedList() {
    // 内部类
    function Node(data) {
        this.data = data;
        this.prev = null;
        this.next = null;
    }
    // 属性
    this.head = null;
    this.tail = null;
    this.length = 0;
    // 操作

    // 1、append方法(增)
    //append(element): 向列表尾部添加一个新的项
    DoublyLinkedList.prototype.append = function (data) {
        // 1、根据data创建节点
        let newNode = new Node(data);

        // 2、判断添加的是否是第一个节点
        if (this.length === 0) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            newNode.prev = this.tail;
            this.tail.next = newNode;
            this.tail = newNode;
        }

        // 3、length++
        this.length += 1;
    }

    // 2、将链表转成字符串形式
    // 2.1、toString方法
    DoublyLinkedList.prototype.toString = function () {
        return this.backwardString();
    }
    // 2.2、forwardString方法
    DoublyLinkedList.prototype.forwardString = function () {
        // 1、定义变量
        let current = this.tail;
        let resultString = '';

        // 2、依次向前遍历，获取每一个节点
        while (current) {
            resultString += current.data + ' ';
            current = current.prev;
        }

        return resultString;
    }
    // 2.3、backwardString方法
    DoublyLinkedList.prototype.backwardString = function () {
        // 1、定义变量
        let current = this.head;
        let resultString = '';

        // 2、依次向后遍历，获取每一个节点
        while (current) {
            resultString += current.data + ' ';
            current = current.next;
        }

        return resultString;
    }

    // 3、insert方法(增)
    // insert(position, element): 项列表的特定位置插入一个新的项
    DoublyLinkedList.prototype.insert = function (position, data) {
        // 1、越界判断
        if (position < 0 || position > this.length) {
            return false;
        }
        // 2、根据data创建新的节点
        const newNode = new Node(data);
        // 3、判断原来的列表是否为空
        if (this.length === 0) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            // 3.1判断position是否为0
            if (position === 0) {
                this.head.prev = newNode;
                newNode.next = this.head;
                this.head = newNode;
            } else if (position === this.length) {
                newNode.prev = this.tail;
                this.tail.next = newNode;
                this.tail = newNode;
            } else {
                let index = 0;
                let current = this.head;
                while (index++ < position) {
                    current = current.next;
                }
                newNode.prev = current.prev;
                newNode.next = current;
                current.prev.next = newNode;
                current.prev = newNode;
            }
        }
        // 4、length+1
        this.length += 1;
        return true;
    }

    // 4、get方法(查)
    // get(position): 获取对应位置的元素
    DoublyLinkedList.prototype.get = function (position) {
        // 1、越界判断
        if (position < 0 || position >= this.length) {
            return null;
        }
        // this.length / 2 > position;从头向后遍历
        // this.length / 2 < position;从后向前遍历
        // 2、获取对应的data
        let index = 0;
        let current = this.head;
        while (index++ < position) {
            current = current.next;
        }

        return current.data;
    }

    // 5、indexOf方法(查)
    DoublyLinkedList.prototype.indexOf = function (data) {
        // 1、定义变量
        let index = 0;
        let current = this.head;
        // 2、查找data
        while (current) {
            if (current.data === data) {
                return index;
            }
            index += 1;
            current = current.next;
        }
        // 3、找不到就返回-1
        return -1;
    }

    // 6、update方法(改)
    //update(position, element):修改某个位置的元素
    DoublyLinkedList.prototype.update = function (position, newData) {
        // 1、越界判断
        if (position < 0 || position >= this.length) {
            return false;
        }
        // 2、找到节点
        let index = 0;
        let current = this.head;
        while (index++ < position) {
            current = current.next;
        }
        // 3、修改该节点数据
        current.data = newData;
        return true;
    }

    // 7、removeAt方法(删)
    // removeAt(position): 从列表的特定位置移除一项.
    DoublyLinkedList.prototype.removeAt = function (position) {
        // 1、判断是否越界
        if (position < 0 || position >= this.length) {
            return null;
        }
        // 2、判断是否只有一个节点
        let current = this.head;//最后要返回current.data，如果写在里面可能为空
        if (this.length === 1) {
            this.head = null;
            this.tail = null;
        } else {
            if (position === 0) {
                this.head.next.prev = null;
                this.head = this.head.next;
            } else if (position === this.length - 1) {
                current = this.tail;
                this.tail.prev.next = null;
                this.tail = this.tail.prev;
            } else {
                let index = 0;
                while (index++ < position) {
                    current = current.next;
                }
                current.prev.next = current.next;
                current.next.prev = current.prev;
            }
        }
        // 3、长度-1
        this.length -= 1;
        return current.data;
    }

    // 8、remove方法(删)
    DoublyLinkedList.prototype.remove = function (data) {
        // 1、根据data先获取data在列表中的位置
        let position = this.indexOf(data);
        // 2、根据位置信息，删除节点
        return this.removeAt(position);
    }

    // 9、isEmpty方法
    DoublyLinkedList.prototype.isEmpty = function () {
        return this.length === 0;
    }

    // 10、size方法
    DoublyLinkedList.prototype.size = function () {
        return this.length;
    }
}
```

## 测试
```js
const list = new DoublyLinkedList();

// 1、append方法
list.append('刘备');
list.append('关羽');
list.append('张飞');
console.log(`append: ${list}`);//append: 刘备 关羽 张飞

// 2、测试转成字符串的方法
console.log(`toString: ${list.toString()}`);//toString: 刘备 关羽 张飞
console.log(`forwardString: ${list.forwardString()}`);//forwardString: 张飞 关羽 刘备
console.log(`backwardString: ${list.backwardString()}`);//backwardString: 刘备 关羽 张飞

// 3、测试insert方法
list.insert(0, '曹操');
list.insert(3, '赵云');
list.insert(5, '诸葛亮');
console.log(`insert: ${list}`);//insert: 曹操 刘备 关羽 赵云 张飞 诸葛亮

// 4、测试get方法
console.log(`get1: ${list.get(0)}`);//曹操
console.log(`get2: ${list.get(3)}`);//赵云
console.log(`get3: ${list.get(5)}`);//诸葛亮

// 5、测试indexOf方法
console.log(`indexOf1: ${list.indexOf('曹操')}`);//0
console.log(`indexOf2: ${list.indexOf('赵云')}`);//3
console.log(`indexOf3: ${list.indexOf('诸葛亮')}`);//5
console.log(`indexOf4: ${list.indexOf('刘禅')}`);// -1

// 6、测试update方法
list.update(0, '曹操6');
list.update(3, '赵云6');
list.update(5, '诸葛亮6');
console.log(`update: ${list}`);//update: 曹操6 刘备 关羽 赵云6 张飞 诸葛亮6

// 7、测试removeAt方法
list.removeAt(0);
list.removeAt(2);
list.removeAt(3);
console.log(`removeAt: ${list}`);

// 8、测试remove方法
list.remove('刘备');
list.remove('关羽');
list.remove('张飞');
console.log(`remove：${list}`);

// 9、测试isEmpty方法
console.log(`isEmpty: ${list.isEmpty()}`);//true

// 10、测试size方法
console.log(`size: ${list.size()}`)//0
```
