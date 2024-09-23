---
title: 从上到下打印二叉树II
date: 2021-04-06 11:23:24
tag: Algorithm
toc: true
---

### 题目描述
>从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

**例如:**
给定二叉树: [3,9,20,null,null,15,7]
```js
    3
   / \
  9  20
    /  \
   15   7
```
返回其层次遍历结果：
```
[
  [3],
  [9,20],
  [15,7]
]
```

**提示：**
节点总数 <= 1000

### 解题思路
>使用将行号作为参数的递归即可。也可以使用队列 BFS 来进行层序遍历。

```js
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
const levelOrder = function (root) {
    if (root === null) return [];
    let ans = [];
    levelOrderArr(root, 0, ans);
    return ans;
}
const levelOrderArr = function (node, k, res) {
    if (node !== null) {
        //k为0时，表示根节点那层，res为空所以长度为0，k为1时，表示根节点的孩子，res这时有根节点在里面，长度为1，所以当k === res的长度时，表明当前层还没有创建,因此推入一个空数组
        if (k === res.length) res.push([]);
        res[k].push(node.val);
        levelOrderArr(node.left, k + 1, res);
        levelOrderArr(node.right, k + 1, res);
    }
}
```