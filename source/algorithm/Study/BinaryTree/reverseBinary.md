---
title: 翻转二叉树
date: 2021-04-06 11:21:24
tag: Algorithm
toc: true
---

### 题目描述
>翻转一棵二叉树

示例：
输入：
```js
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```
输出：
```js
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

### 解题思路
>解题思路：交换左右子树，再递归翻转左右子树。

```js
/**
 * @param {TreeNode} root
 * @return {TreeNode}
 */
 var invertTree = function(root) {
    if (root === null) return null;
    [root.left, root.right] = [root.right, root.left];
    invertTree(root.left);
    invertTree(root.right);
    return root;
};
```