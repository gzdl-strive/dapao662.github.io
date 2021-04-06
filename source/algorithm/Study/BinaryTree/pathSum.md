---
title: 路径总和
date: 2021-04-06 14:35:24
tag: Algorithm
toc: true
---

### 题目描述
>给你二叉树的根节点 root 和一个表示目标和的整数 targetSum ，判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。叶子节点 是指没有子节点的节点

示例 1：
输入：root = [1,2], targetSum = 0
输出：false

**提示：**
树中节点的数目在范围 [0, 5000] 内
-1000 <= Node.val <= 1000
-1000 <= targetSum <= 1000

### 解题思路
>使用递归思想，函数意义，判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum

```js
/**
 * @param {TreeNode} root
 * @param {number} targetSum
 * @return {boolean}
 */
var hasPathSum = function(root, targetSum) {
    //处理边界问题
    if (root === null) return false;
    //判断是否为叶子节点，如果是且targetSum = 当前节点的值=》返回true
    if (root.left === null && root.right === null) {
        return targetSum === root.val;
    }
    targetSum -= root.val;
    return hasPathSum(root.left, targetSum) || hasPathSum(root.right, targetSum)
};
```
