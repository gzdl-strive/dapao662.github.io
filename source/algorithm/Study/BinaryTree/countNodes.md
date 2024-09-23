---
title: 完全二叉树的节点个数
date: 2021-04-06 14:44:24
tag: Algorithm
toc: true
---

### 题目描述：
>给你一棵 完全二叉树 的根节点 root ，求出该树的节点个数。
完全二叉树 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。

输入：root = [1,2,3,4,5,6]
输出：6

输入：root = []
输出：0

输入：root = [1]
输出：1

树中节点的数目范围是[0, 5 * 104]
0 <= Node.val <= 5 * 104
题目数据保证输入的树是 完全二叉树

进阶：遍历树来统计节点是一种时间复杂度为 O(n) 的简单解决方案。你可以设计一个更快的算法吗？

### 解决方案
递归思想，递归函数的意义是:返回当前树的节点个数

```js
/**
 * @param {TreeNode} root
 * @return {number}
 */
const countNodes = function (root) {
    if (!root) return 0;
    return countNodes(root.left) + countNodes(root.right) + 1;
}
```