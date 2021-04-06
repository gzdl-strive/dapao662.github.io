---
title: 二叉树的锯齿形层序遍历
date: 2021-04-06 14:23:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一个二叉树，返回其节点值的锯齿形层序遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

例如：
给定二叉树 [3,9,20,null,null,15,7],
```js
    3
   / \
  9  20
    /  \
   15   7
```
返回锯齿形层序遍历如下：
```js
[
  [3],
  [20,9],
  [15,7]
]
```

### 解题思路
>与从上到下打印二叉树II和二叉树的层序遍历那两题相似，使用将行号作为参数的递归即可，只不过后面碰到k是奇数的情况把里面的元素反转一下即可

```js
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
 var zigzagLevelOrder = function(root) {
    if (root === null) return [];
    let ans = [];
    zigzagLevelOrderArr(root, 0, ans);
    for (let i = 0; i < ans.length; i++) {
        if (i % 2 === 1) {
            let arr = ans[i];
            let m = 0, n = arr.length - 1;
            while (m < n) {
                [arr[m], arr[n]] = [arr[n], arr[m]];
                m++;
                n--;
            }
        }
    }
    return ans;
};
const zigzagLevelOrderArr = function (node, k, res) {
    if (node !== null) {
        if (k === res.length) res.push([]);
        res[k].push(node.val);
        zigzagLevelOrderArr(node.left, k + 1, res);
        zigzagLevelOrderArr(node.right, k + 1, res);
    }
}
```