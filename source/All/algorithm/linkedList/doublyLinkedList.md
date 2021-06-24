---
title: 双向链表及其功能实现
date: 2021-06-15 10:26:24
toc: true
---

## 认识双向链表

### 单向链表
* 只能从头遍历到尾或者从尾遍历到头(一般从头到尾)
* 也就是链表相连的过程是单向的
* 实现的原理是上一个链表中有指向下一个的引用

**单向链表的缺点**
我们可以轻松的到达下一个节点，但是回到上一个节点是很难的。但是，实际开发中，经常会遇到需要回到上一个节点的情况
举个例子： 假设一个文本编辑用链表来存储文本。每一行用一个String对象存储在链表的一个节点中,当编辑器用户向下移动光标时，链表直接操作到下一个节点即可。但是当用于将光标向上移动呢？在这个时候为了回到上一个节点，我们可能需要从first开始，依次走到想要的节点上

### 双向链表
* 既可以从头遍历到尾，又可以从尾遍历到头
* 也就是链表相连的过程是双向的。
* 一个节点既有向前连接的引用，也有一个向后连接的引用.
* 双向链表可以有效的解决单向链表中提到的问题

**双向链表有什么缺点呢?**
* 每一在插入或删除某个节点时，需要处理四个引用,而不是两个.也就是实现起来要困难一些
* 并且相当于单向链表，必然占用内存空间更大一些
* 但是这些缺点和我们使用起来的方便程度相比，是微不足道的

**双向链表的特点**
* 可以使用一个head和一个tail分别指向链表的头部和尾部
* 每个节点都由三个部分组成：前一个节点的指针(prev)/报存的元素(item)/后一个节点的指针(next)
* 双向链表的第一个节点的prev指针指向null
* 双向链表的最后一个节点的next指针指向null

## 双向链表类的实现
双向链表的方法和单向链表差不多,只不过新增两个遍历的方法，一个向前遍历(forwardString)，一个向后遍历(backwardString)
```js
function DoublyLinkedList() {
  //内部类
  function Node(data) {
    this.prev = null;
    this.data = data;
    this.next = null;
  }
  //属性
  this.head = null;
  this.tail = null;
  this.length = 0;

  //操作
  //1、append方法(增)
  DoublyLinkedList.prototype.append = function(data) {
    //1、根据data创建新节点
    let newNode = new Node(data);
    //2、判断添加的是否是第一个节点
    if (this.length === 0) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      //不是第一个节点，则添加到最后一个节点的后面，即尾指针指向的节点后
      newNode.prev = this.tail;
      this.tail.next = newNode;
      this.tail = newNode;
    }
    //3、修改链表长度
    this.length += 1;
  }

  //2、将链表转成字符串形式
  DoublyLinkedList.prototype.toString = function() {
    return this.backwardString();
  }
  //2.1、forwardString方法
  DoublyLinkedList.prototype.forwardString = function () {
    //1、定义变量
    let current = this.tail;
    let resultString = '';
    //2、依次向前遍历，获取每一个节点
    while (current) {
      resultString += current.data + ' ';
      current = current.prev;
    }
    //3、返回结果
    return resultString;
  }
  //2.2、backwardString方法
  DoublyLinkedList.prototype.backwardString = function () {
    //1、定义变量
    let current = this.head;
    let resultString = '';
    //2、依次向后遍历，获取每一个节点
    while (current) {
      resultString += current.data + ' ';
      current = current.next;
    }
    //3、返回结果
    return resultString;
  }
  
  //3、insert方法(增)
  DoublyLinkedList.prototype.insert = function (position, data) {
    //1、判断是否越界
    if (position < 0 || position > this.length) {
      return false;
    }
    //2、根据data创建新节点
    let newNode = new Node(data);
    //3、判断原来的链表是否为空
    if (this.length === 0) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      //3.1 判断position是否为0
      if (position === 0) {
        this.head.prev = newNode;
        newNode.next = this.head;
        this.head = newNode;
      } else if (position === this.length) {
        this.tail.next = newNode;
        newNode.prev = this.tail;
        this.tail = newNode;
      } else {
        let index = 0;
        let current = this.head;
        while (index++ < position) {
          current = current.next;
        }
        //执行到此，说明找到了节点
        //要在current和current的前一个节点之间插入这个新节点
        //需要改变的由current前一个节点的next、current的prev、新节点的prev和next
        newNode.prev = current.prev;
        newNode.next = current;
        current.prev.next = newNode;
        current.prev = newNode;
      }
    }
    //4、修改链表长度
    this.length += 1;
    return true;
  }

  //4、get方法（查）
  DoublyLinkedList.prototype.get = function (position) {
    //1、判断是否越界
    if (position < 0 || position > this.length - 1) {
      return null;
    }
    //2、获取对应的data
    let index = 0;
    let current = this.head;
    while (index++ < position) {
      current = current.next;
    }
    //3、找到positon对应的节点了，返回它
    return current.data;
  }

  //5、indexOf方法(查)
  DoublyLinkedList.prototype.indexOf = function(data) {
    //1、定义变量
    let index = 0;
    let current = this.head;
    //2、循环查找
    while (current) {
      if (current.data === data) {
        return index;
      }
      index += 1;
      current = current.next;
    }
    //3、循环结束了都没有找到，返回-1
    return -1;
  }

  //6、update方法(改)
  DoublyLinkedList.prototype.update = function(position, newData) {
    //1、越界判断
    if (position < 0 || position > this.length - 1) {
      return false;
    }
    //2、找到节点
    let index = 0;
    let current = this.head;
    while (index++ < position) {
      current = current.next;
    }
    //3、找到了，修改该节点的data
    current.data = newData;
    return true;
  }

  //7、removeAt方法(删)
  DoublyLinkedList.prototype.removeAt = function(position) {
    //1、越界判断
    if (position < 0 || position > this.length - 1 || this.length === 0) {
      return null;
    }
    //2、判断是否只有一个节点
    let current = this.head;//最后要返回current.data，如果写在里面可能为空
    if (this.length === 1) {
      this.head = null;
      this.tail = null;
    } else {
      //2.1判断要删除的是否是第一个节点
      if (position === 0) {
        this.head.next.prev = null;
        this.head = this.head.next;
      } else if(position === this.length - 1){
        //2.2判断要删除的是否是最后一个节点
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
    //3、修改链表长度
    this.length -= 1;
    return current.data;
  }

  //8、remove方法(删)
  DoublyLinkedList.prototype.remove = function(data) {
    //1、根据data先获取data在链表中的位置
    let position = this.indexOf(data);
    //根据位置删除该节点
    if (position !== -1) {
      return this.removeAt(position);
    }
  }

  //9、isEmpty方法
  DoublyLinkedList.prototype.isEmpty = function() {
    return this.length === 0;
  }

  //10、size方法
  DoublyLinkedList.prototype.size = function() {
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
