---
title: 二叉搜索树-TypeScript
date: 2022-07-10 22:09:24
toc: true
---

## 二叉搜索树类型
```ts
export type BinarySearchTreeNodeType = number;
export type HandlerType = (key: BinarySearchTreeNodeType) => void;
```

## 二叉搜索树节点
```ts
import { BinarySearchTreeNodeType } from './types';

class BinarySearchTreeNode {
  key: BinarySearchTreeNodeType;
  left: BinarySearchTreeNode | null;
  right: BinarySearchTreeNode | null;
  constructor(data: BinarySearchTreeNodeType) {
    this.key = data;
    this.left = null;
    this.right = null;
  }
}

export default BinarySearchTreeNode;
```

## 封装二叉搜索树
```ts
import BinarySearchTreeNode from "./BinarySearchTreeNode";
import { BinarySearchTreeNodeType, HandlerType } from './types';

class BinarySearchTree {
  root: BinarySearchTreeNode | null;
  length: number;
  constructor() {
    this.root = null;
    this.length = 0;
  }

  // 1、插入数据: 对外给用户调用的方法
  insert(key: BinarySearchTreeNodeType) {
    // 1、根据key创建节点
    const newNode = new BinarySearchTreeNode(key);
    // 2、判断根节点是否有值
    if (!this.root) {
      this.root = newNode;
    } else {
      // 因为我们需要递归的去寻找到需要插入的位置，所以通过新创建的一个方法来进行递归调用
      this.insertNode(this.root, newNode);
    }
  }
  // 插入数据的递归调用方法
  insertNode(node: BinarySearchTreeNode, newNode: BinarySearchTreeNode) {
    // 如果新插入节点的值小于当前节点，那么应该插入到左子树
    if (newNode.key < node.key) {
      // 这边还存在两种情况，左子树为空与不为空
      if (!node.left) {
        node.left = newNode;
      } else {
        // 不为空继续递归调用
        this.insertNode(node.left, newNode);
      }
    } else {
      // 将新节点插入到右子树
      // 也存在两种情况，右子树是否为空
      if (!node.right) {
        node.right = newNode;
      } else {
        this.insertNode(node.right, newNode);
      }
    }
  }
  // 2、树的遍历
  // 前序遍历(根、左、右)
  preOrderTraversal(handler: HandlerType) {
    this.preOrderTraversalNode(this.root, handler);
  }
  preOrderTraversalNode(node: BinarySearchTreeNode | null, hanlder: HandlerType) {
    // 边界条件,当前节点存在
    if (node) {
      // 1、查找经过的节点
      hanlder(node.key);
      // 2、前序遍历左子树
      this.preOrderTraversalNode(node.left, hanlder);
      // 3、前序遍历右子树
      this.preOrderTraversalNode(node.right, hanlder);
    }
  }
  // 中序遍历
  midOrderTraversal(handler: HandlerType) {
    this.midOrderTraversalNode(this.root, handler);
  }
  midOrderTraversalNode(node: BinarySearchTreeNode | null, handler: HandlerType) {
    if (node) {
      this.midOrderTraversalNode(node.left, handler);
      handler(node.key);
      this.midOrderTraversalNode(node.right, handler);
    }
  }
  // 后序遍历
  postOrderTraversal(handler: HandlerType) {
    this.postOrderTraversalNode(this.root, handler);
  }
  postOrderTraversalNode(node: BinarySearchTreeNode | null, handler: HandlerType) {
    if (node) {
      this.postOrderTraversalNode(node.left, handler);
      this.postOrderTraversalNode(node.right, handler);
      handler(node.key);
    }
  }

  // 3、寻找最值
  // 最大值
  max() {
    // 空树返回Null
    if (!this.root) return null;
    // 从根节点往下寻找
    let node: BinarySearchTreeNode | null = this.root, key: BinarySearchTreeNodeType = node.key;
    while (node) {
      key = node.key;
      node = node.right;
    }
    return key;
  }
  // 最小值
  min() {
    if (!this.root) return null;
    let node: BinarySearchTreeNode | null = this.root, key: BinarySearchTreeNodeType = node.key;
    while (node) {
      key = node.key;
      node = node.left;
    }
    return key;
  }

  // 4、搜索
  // 递归搜索
  search(key: BinarySearchTreeNodeType) {
    // 1、如果为空树，直接返回false
    if (!this.root) return false;
    const result = this.searchNode(this.root, key);
    return result;
  }
  searchNode(node: BinarySearchTreeNode | null, key: BinarySearchTreeNodeType): boolean {
    if (!node) return false;
    if (key < node.key) {
      // 向左查找
      return this.searchNode(node.left, key);
    } else if (key > node.key) {
      // 向右查找
      return this.searchNode(node.right, key);
    } else {
      // 找到了，返回true
      return true;
    }
  }
  // 循环搜索
  loopSearch(key: BinarySearchTreeNodeType): boolean {
    if (!this.root) return false;
    // 循环搜索key
    let node: BinarySearchTreeNode | null = this.root;
    while (node) {
      if (key < node.key) {
        node = node.left;
      } else if (key > node.key) {
        node = node.right;
      } else {
        return true;
      }
    }
    // 循环结束还没返回，说明没有找到，返回false
    return false;
  }

  // 5、删除
  remove(key: BinarySearchTreeNodeType) {
    // 空树，直接返回Null
    if (!this.root) return null;
    // 1、定义遍历，保持信息
    let current: BinarySearchTreeNode | null = this.root;
    let parent: BinarySearchTreeNode | null = this.root;
    let isLeftChild: boolean = true;

    // 寻找要删除的节点
    while (current.key !== key) {
      parent = current;
      if (key < current.key) {
        isLeftChild = true;
        current = current.left;
      } else {
        isLeftChild = false;
        current = current.right;
      }
      // 找到最后了，依然没有找到，直接返回false
      if (current === null) {
        return false;
      }
    }

    // 循环结束，current就是需要删除的节点，parent为需要删除的节点的父节点

    // 2、根据对应情况删除节点
    // 2.1、删除的节点是叶子节点(没有子节点)
    if (!current.left && !current.right) {
      // 1、判断要删除的是否是根节点
      if (current === this.root) {
        this.root = null;
      } else if (isLeftChild) {
        parent.left = null;
      } else {
        parent.right = null;
      }
    } else if (current.left === null) {
      // 删除的节点中还有一个子节点,存在右子树
      if (current === this.root) {
        this.root = current.right;
      } else if (isLeftChild) {
        parent.left = current.right;
      } else {
        parent.right = current.right;
      }
    } else if (current.right === null) {
      // 删除的节点中还有一个子节点,存在左子树
      if (current === this.root) {
        this.root = current.left;
      } else if (isLeftChild) {
        parent.left = current.left;
      } else {
        parent.right = current.left;
      }
    } else {
      // 删除的节点中还有俩个子节点(度为2)
      // 获取待删除节点的后继节点
      // 使用后继
      // let successor = this.getSuccessor(current);
      // if (current === this.root) {
      //   this.root = successor;
      // } else if (isLeftChild) {
      //   parent.left = successor;
      // } else {
      //   parent.right = successor;
      // }
      // 因为删除的节点还存在left节点，所以将后继节点的left指针指向删除节点的left
      // successor.left = current.left;

      // 使用前驱
      let precursor = this.getPrecursor(current);
      if (current === this.root) {
        this.root = precursor;
      } else if (isLeftChild) {
        parent.left = precursor;
      } else {
        parent.right = precursor;
      }
      precursor.right = current.right;
    }
  }

  // 6、找后继结点->右子树中最小的
  getSuccessor(delNode: BinarySearchTreeNode) {
    // 定义变量
    let successor: BinarySearchTreeNode = delNode; // 后继节点
    let successorParent: BinarySearchTreeNode = delNode; // 后继节点的父节点
    let current: BinarySearchTreeNode | null = delNode.right; // 变量-》用来遍历

    // 循环查找
    while (current) {
      successorParent = successor;
      successor = current;
      current = current.left;
    }

    // 判断寻找到的后继节点是否直接就是delNode的右节点
    if (successor !== delNode.right) {
      // 后继节点的父节点的left指向后继节点(后继节点是最小的，所以只能是在left中)
      // 后继节点是没有left节点的，因为有的话，后继节点就不会是它，只可能存在right节点

      // 所以，这边将后继节点的父节点的left指向后继节点的right节点(可能为null)
      successorParent.left = successor.right;
      // 后继节点的right指向delNode的right节点
      successor.right = delNode.right;
    }
    return successor;
  }

  // 7、找前驱节点->左子树中最大的
  getPrecursor(delNode: BinarySearchTreeNode) {
    // 定义变量
    let precursor: BinarySearchTreeNode = delNode;
    let precursorParent: BinarySearchTreeNode = delNode;
    let current: BinarySearchTreeNode | null = delNode.left;

    // 循环查找
    while (current) {
      precursorParent = precursor;
      precursor = current;
      current = current.right;
    }

    // 判断前驱节点是否直接就是delNode的左节点
    // 不是delNode的左节点，需要变换一下
    if (precursor !== delNode.left) {
      // 前驱节点的父节点的right指针指向前驱节点
      // 前驱节点只能存在左子节点，不可能存在右子节点
      precursorParent.right = precursor.left;
      precursor.left = delNode.left;
    }
    return precursor;
  }
}

export default BinarySearchTree;
```

## 测试
```ts
import BinarySearchTree from "./BinarySearchTree";
import { BinarySearchTreeNodeType } from './types';

// 测试一下
const bst = new BinarySearchTree();

// 插入数据
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

// 测试遍历
let result: string = '';
const outputBST = (key: BinarySearchTreeNodeType) => {
  result += key + ' ';
}

// 前序遍历
bst.preOrderTraversal(outputBST);
console.log('preorder: ', result);

// 中序遍历-二叉搜索树的中序遍历就相当于排序(从小到大)
result = '';
bst.midOrderTraversal(outputBST);
console.log('midOrder: ', result);

// 后序遍历
result = '';
bst.postOrderTraversal(outputBST);
console.log('postOrder: ', result);

// 最值
console.log('max: ', bst.max());
console.log('min: ', bst.min());

// 搜索
console.log('search: ', bst.search(8));
console.log('search: ', bst.search(16));
console.log('loopSearch', bst.loopSearch(8));
console.log('loopSearch', bst.loopSearch(16));

// 删除
// bst.remove(3);
// console.log('min: ', bst.min());
// result = '';
// bst.midOrderTraversal(outputBST);
// console.log('midOrder: ', result);
// bst.remove(5);
// console.log('min: ', bst.min());
// result = '';
// bst.midOrderTraversal(outputBST);
// console.log('midOrder: ', result);
bst.remove(7);
result = '';
bst.postOrderTraversal(outputBST);
console.log('postOrder: ', result);
```