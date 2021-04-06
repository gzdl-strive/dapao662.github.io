---
title: 二叉树的层序遍历II
date: 2021-04-06 14:25:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一个二叉树，返回其节点值自底向上的层序遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

例如：
给定二叉树 [3,9,20,null,null,15,7],
```js
    3
   / \
  9  20
    /  \
   15   7
```
返回其自底向上的层序遍历为：
```js
[
  [15,7],
  [9,20],
  [3]
]
```

### 解题思路
>和从上到下打印二叉树II那题相似，使用将行号作为参数的递归即可，只不过后面还需要反转一下，反转可以用双指针

```js
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrderBottom = function(root) {
    if (root === null) return [];
    let ans = [];
    levelOrderBottomArr(root, 0, ans);
    let i = 0, j = ans.length - 1;
    while (i < j) {
        [ans[i], ans[j]] = [ans[j], ans[i]];
        i++;
        j--;
    }
    return ans;
};
const levelOrderBottomArr = function (node, k, res) {
    if (node !== null) {
        if (k === res.length) res.push([]);
        res[k].push(node.val);
        levelOrderBottomArr(node.left, k + 1, res);
        levelOrderBottomArr(node.right, k + 1, res);
    }
}
```
