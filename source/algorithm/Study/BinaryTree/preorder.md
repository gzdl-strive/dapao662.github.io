---
title: 二叉树的前序遍历
date: 2021-04-06 10:59:24
tag: Algorithm
toc: true
---

### 题目描述
>给你二叉树的根节点 root ，返回它节点值的 **前序** 遍历。

**示例**
示例1：
输入：root = [1,null,2,3]
输出：[1,2,3]
示例 2：
输入：root = []
输出：[]
示例 3：
输入：root = [1]
输出：[1]

**提示**
树中节点数目在范围 [0, 100] 内
-100 <= Node.val <= 100

### 解题思路
>递归遍历

```js
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var preorderTraversal = function (root) {
    let ans = [];
    if (root === null) {
        return ans;
    }
    preorder(root, ans);
    return ans;
};
const preorder = function (node, res) {
    if (node !== null) {
        res.push(node.val);
        preorder(node.left, res);
        preorder(node.right, res);
    }
}
```

