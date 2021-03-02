---
title: 二叉搜索树的操作实现
date: 2021-03-02 10:06:02
tag: Algorithm
---

>二叉搜索树的常见操作
insert(key): 向树中插入一个新的键
search(key): 在树中查找一个键，如果节点存在，则返回true,否则返回false
inOrderTraverse: 通过中序遍历方式遍历所有节点
preOrderTraverse: 通过先序遍历方式遍历所有节点
postOrderTraverse: 通过后序遍历方式遍历所有节点
min: 返回树中最小的值/键
max: 返回树中最大的值/键
remove(key):从树中移除某个键

### 遍历
**注意我们这里学习的树的遍历，针对所有的二叉树都是适用的,不仅仅是二叉搜索树**

>二叉树的遍历常见的三种方式:
1、先序遍历
2、中序遍历
3、后序遍历
(还有层序遍历，使用较少，可以使用队列来完成)

**先序遍历：** 
 1、遍历根节点
 2、先序遍历左子树
 3、先序遍历右子树
**中序遍历：**
 1、中序遍历其左子树
 2、访问根节点
 3、中序遍历其右子树
**后序遍历**
 1、后序遍历其左子树
 2、后序遍历其右子树
 3、访问根节点

### 封装二叉搜索树类
```js
function BinarySearchTree() {
    // 内部类
    function Node(key) {
        this.key = key;
        this.left = null;
        this.right = null;
    }
    // 属性
    this.root = null;
    // 方法
    // 1、插入数据：对外给用户调用的方法
    BinarySearchTree.prototype.insert = function (key) {
        // 1、根据key创建节点
        let newNode = new Node(key);
        // 2、判断根节点是否有值
        if (this.root === null) {
            this.root = newNode;
        } else {
            this.insertNode(this.root, newNode);
        }
    }
    // 插入方法中递归调用的方法(在内部使用)
    // 节点之间的比较
    BinarySearchTree.prototype.insertNode = function (node, newNode) {
        if (newNode.key < node.key) {
            // 向左查找
            if (node.left === null) {
                // 左节点是否为空
                node.left = newNode;
            } else {
                this.inertNode(node.left, newNode);
            }
        } else {
            // 向右查找
            if (node.right === null) {
                // 右节点是否为空
                node.right = newNode;
            } else {
                this.insertNode(node.right, newNode);
            }
        }
    }

    // 树的遍历
    // 1、先序遍历
    BinarySearchTree.prototype.preOrderTraversal = function (handler) {
        this.preOrderTraversalNode(this.root, handler);
    }
    BinarySearchTree.prototype.preOrderTraversalNode = function (node, handler) {
        if (node !== null) {
            // 1、查找经过的节点
            handler(node.key);
            // 2、查找经过的左子节点
            this.preOrderTraversalNode(node.left, handler);
            // 3、查找经过的右子节点
            this.preOrderTraversalNode(node.right, handler);
        }
    }

    // 2、中序遍历
    BinarySearchTree.prototype.midOrderTraversal = function (handler) {
        this.midOrderTraversalNode(this.root, handler);
    }
    BinarySearchTree.prototype.midOrderTraversalNode = function (node, handler) {
        if (node !== null) {
            // 1、查找左子树中的节点
            this.midOrderTraversalNode(node.left, handler);
            // 2、查找节点
            handler(node.key);
            // 3、查找右子树的节点
            this.midOrderTraversalNode(node.right, handler);
        }
    }
    
    // 3、后序遍历
    BinarySearchTree.prototype.postOrderTraversal = function (handler) {
        this.postOrderTraversalNode(this.root, handler);
    }
    BinarySearchTree.prototype.postOrderTraversalNode = function (node, handler) {
        if (node !== null) {
            // 1、查找左子树中的节点
            this.postOrderTraversalNode(node.left, handler);
            // 2、查找右子树中的节点
            this.postOrderTraversalNode(node.right, handler);
            // 3、查找节点
            handler(node.key);
        }
    }

    // 寻找最值
    BinarySearchTree.prototype.max = function () {
        // 1、获取根节点
        let node = this.root;
        // 2、依次向下查找，直到节点为null为之
        let key = null;
        while (node !== null) {
            key = node.key;
            node = node.right;
        }
        return key;
    }
    BinarySearchTree.prototype.min = function () {
        // 1、获取根节点
        let node = this.root;
        // 2、依次向下查找，直到节点为null为之
        let key = null;
        while (node !== null) {
            key = node.key;
            node = node.left;
        }
        return key;
    }
}
```

### 测试
```js
// 测试
// 1、创建BinarySearchTree
const bst = new BinarySearchTree();
// 2、插入数据
bst.insert(11);
bst.insert(7);
bst.insert(15);
bst.insert(5);
bst.insert(3);
bst.insert(9);
bst.insert(8);
bst.insert(10);
bst.insert(13);
bst.insert(12);
bst.insert(14);
bst.insert(20);
bst.insert(18);
bst.insert(25);
bst.insert(6);

// 3、测试遍历
// 3.1测试先序遍历
let resultString = '';
bst.preOrderTraversal(function (key) {
    resultString += key + ' ';
})
console.log(resultString);
// 3.2测试中序遍历
let resultString2 = '';
bst.midOrderTraversal(function (key) {
    resultString2 += key + ' ';
})
console.log(resultString2);
// 3.2测试后序遍历
let resultString3 = '';
bst.postOrderTraversal(function (key) {
    resultString3 += key + ' ';
})
console.log(resultString3);

// 4、测试最值
let max = bst.max();
let min = bst.min();
console.log(`最小值: ${min}`)
console.log(`最大值: ${max}`)
```
