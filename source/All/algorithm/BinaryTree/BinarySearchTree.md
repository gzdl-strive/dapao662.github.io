---
title: 二叉搜索树
date: 2021-06-28 10:21:24
toc: true
---

## 初步封装
```js
function BinarySearchTree() {
  //内部类
  function Node(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  }
  //属性
  this.root = null;
  //方法
}
```

## 二叉搜索树的常见操作
>insert(key): 向树中插入一个新的键
search(key): 在树中查找一个键，如果节点存在，则返回true,否则返回false
inOrderTraverse: 通过中序遍历方式遍历所有节点(左子树、根、右子树)
preOrderTraverse: 通过先序遍历方式遍历所有节点(根、左子树、右子树)
postOrderTraverse: 通过后序遍历方式遍历所有节点(左子树、右子树、根)
min: 返回树中最小的值/键
max: 返回树中最大的值/键
remove(key):从树中移除某个键

## 二叉树的删除操作
>删除节点从要查找的节点开始，找到节点后，需要考虑三种情况：
1、该节点是叶子节点(没有子节点、比较简单)
2、该节点有一个子节点
3、该节点有两个子节点

**情况一、没有子节点**
* 我们需要检查current的left和right是否都为null
* 都为null之后还有检查一个东西，就是是否current就是根节点,如果是，相当于清空了根
* 否则直接把父节点的left或right设置为Null即可

**情况二、有一个子节点**

**情况三、有两个子节点**
如果我们要删除的节点有两个子节点，甚至子节点还有子节点，这种情况下我们需要从下面的子节点中找到一个节点，来替换当前的节点
但是找到的这个节点有什么特征呢？
**应该是current节点下面所有节点中最接近current节点的**
要么比current节点小一点点，要么比current节点大一点点
总结你最接近current，你可以用来替换current的位置
这个节点怎么找呢？

* 比current小一点点的节点，一定是current左子树的最大值
* 比current大一点点的节点，一定是current右子树的最小值
**前驱和后继**
在二叉搜索树中，这两个特别的节点，有两个特别的名字
* 比current小一点点的节点，称为current节点的前驱
* 比current大一点点的节点，称为current节点的后继

## 封装二叉搜索树类
```js
function BinarySearchTree() {
  //内部类
  function Node(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  }
  //属性
  this.root = null;
  //方法
  //1、插入数据，对外给用户调用的方法
  BinarySearchTree.prototype.insert = function (key) {
    //1、根据key创建节点
    let newNode = new Node(key);
    //2、判断根节点是否有值
    if (this.root === null) {
      this.root = newNode;
    } else {
      //找到合适的位置插入
      this.insertNode(this.root, newNode);
    }
  }
  //插入方法种递归调用的方法(内部使用)
  BinarySearchTree.prototype.insertNode = function(node, newNode) {
    //判断新节点的键值来确定插入到左子树还是右子树
    if (newNode.key < node.key) {
      //向左查找
      if (node.left === null) {
        //左节点为空，直接插入
        node.left = newNode;
      } else {
        //左节点不为空
        this.insertNode(node.left, newNode);
      }
    } else {
      //向右查找
      if (node.right === null) {
        node.right = newNode; 
      } else {
        this.insertNode(node.rigth, newNode);
      }
    }
  }
  //树的遍历
  //1、先序遍历
  BinarySearchTree.prototype.preOrderTraversal = function (handler) {
    this.preOrderTraversalNode(this.root, handler);
  }
  //先序遍历递归函数
  BinarySearchTree.prototype.preOrderTraversalNode = function (node, handler) {
    if (node !== null) {
      //1、查找经过的节点
      handler(node.key);
      //2、查找经过的节点的左子树
      this.preOrderTraversalNode(node.left, handler);
      //3、查找经过的节点的右子树
      this.preOrderTraversalNode(node.right, handler);
    }
  }
  //2、中序遍历
  BinarySearchTree.prototype.midOrderTraversal = function (handler) {
    this.midOrderTraversalNode(this.root, handler);
  }
  BinarySearchTree.prototype.midOrderTraversalNode = function (node, handler) {
    if (node !== null) {
      //1、查找经过节点左子树
      this.midOrderTraversalNode(node.left, handler);
      //2、查找节点
      handler(node.key);
      //3、查找经过节点的右子树
      this.midOrderTraversalNode(node.right, handler);
    }
  }
  //后序遍历
  BinarySearchTree.prototype.postOrderTraversal = function (handler) {
    this.postOrderTraversalNode(this.root, handler);
  }
  BinarySearchTree.prototype.postOrderTraversalNode = function (node, handler) {
    if (node !== null) {
      //1、查找经过节点左子树
      this.postOrderTraversalNode(node.left, handler);
      //2、查找经过节点的右子树
      this.postOrderTraversalNode(node.right, handler);      
      //3、查找节点
      handler(node.key);
    }
  }
  //寻找最大值
  BinarySearchTree.prototype.max = function () {
    //1、获取根节点
    let node = this.root;
    //依次向下查找，直到节点为null为止
    let key = null;
    while (node !== null) {
      key = node.key;
      node = node.right;
    }
    return key;
  }
  //寻找最小值
  BinarySearchTree.prototype.min = function() {
    //1、获取根节点
    let node = this.root;
    //依次向下查找，直到节点为null为止
    let key = null;
    while (node !== null) {
      key = node.key;
      node = node.left;
    }
    return key;
  }
  //递归实现搜索
  BinarySearchTree.prototype.search = function(key) {
    this.searchNode(this.root, key);
  }
  //搜索递归函数
  BinarySearchTree.prototype.searchNode = function(node, key) {
    //1、如果传入的节点为null,那么直接返回false
    if (node === null) {
      return false;
    }
    //2、判断node节点的值和key大小
    if (node.key > key) {
      //2.1传入的key较小，向左查找
      return this.searchNode(node.left, key);
    } else if (node.key < key) {
      //2.2传入的key较大，向右查找
      return this.searchNode(node.right, key);
    } else {
      //2.3传入的key和当前节点的键值一样大
      return true;
    }
  }
  //循环实现搜索
  BinarySearchTree.prototype.search2 = function(key) {
    //1、获取根节点
    let node = this.root;
    //2、循环搜索key
    while (node !== null) {
      if (node.key < key) {
        node = node.right;
      } else if (node.key > key) {
        node = node.left;
      } else {
        return true;
      }
    }
    //3、循环结束还没找到，返回false
    return false;
  }
  //删除操作
  BinarySearchTree.prototype.remove = function(key) {
    //1、先寻找要删除的节点
    //1.1定义变量，保存信息
    let current = this.root;
    let parent = null;
    let isLeftChild = true;
    //1.2
    //寻找要删除的节点
    while (curry.key !== key) {
      parent = current;
      if (key < current.key) {
        isLeftChild = true;
        current = current.left;
      } else {
        isLeftChild = false;
        current = current.right;
      }
      //某种情况：已经找到了最后的节点，依然没有找到=key的节点
      if (current === null) {
        return false;
      }
    }
    //循环结束=》说明current就是要删除的节点
    //2、根据对应情况删除节点
    //2.1删除的节点是叶子节点(没有子节点)
    if (current.left === null && current.right === null) {
      if (current === this.root) {
        this.root = null;
      } else if (isLeftChild) {
        parent.left = null;
      } else {
        parent.right = null;
      }
    } else if (current.right === null) {
      //2.2删除的节点有一个子节点
      if (current === this.root) {
        this.root = current.left;
      } else if (isLeftChild) {
        parent.left = current.left;
      } else {
        parent.right = current.left;
      }
    } else if (current.left === null) {
      if (current === this.root) {
        this.root = current.right;
      } else if (isLeftChild) {
        parent.left = current.right;
      } else {
        parent.right = current.right;
      }
    } else {
      //2.3删除的节点有两个子节点
      //2.3.1获取后继节点
      let succssor = this.getSuccssor(current);
      //2.3.2判断是否是根节点
      if (current === this.root) {
        this.root = succssor;
      } else if (isLeftChild) {
        parent.left = succssor;
      } else {
        parent.right = succssor;
      }
      //2.3.3将后继节点的left指向删除节点的left
      succssor.left = current.left;
    }
  }
  //找后继的方法
  BinarySearchTree.prototype.getSuccssor = function(delNode) {
    //1、定义变量，保存找到的后继节点->右子树的最小值
    let succssor = delNode;
    let current = delNode.right;
    let succssorParent = delNode;//后继节点的父节点
    //2、循环查找
    while (current !== null) {
      succssorParent = succssor;
      succssor = current;
      current = current.left;
    }
    // 3、判断寻找到的后继节点是否直接就是delNode的right节点(可能不是直接连接在delNode的right上)
    if (succssor !== delNode.right) {
    // 后继节点的父节点的left指向后继节点(后继节点是最小的，只能在left中)
    // 后继节点是没有left节点的，因为有的话，后继节点就不会是他，而是它的left节点，所以后继节点只可能有right节点
      succssorParent.left = succssor.right;
      succssor.right = delNode.right;
    }
    return succssor;
  }
}
```