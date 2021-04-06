---
title: 平衡二叉树
date: 2021-04-06 14:34:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一个二叉树，判断它是否是高度平衡的二叉树。
本题中，一棵高度平衡二叉树定义为：一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 。

示例 1:
```js
    3
   / \
   9  20
      /\
    15  7
```
输入：root = [3,9,20,null,null,15,7]
输出：true
示例 2：
输入：root = []
输出：true

**提示：**
树中的节点数在范围 [0, 5000] 内
-104 <= Node.val <= 104

### 解题思路：使用递归方法
>获取当前节点的树高：递归遍历其左右子树的树高，获取左右子树较大的树高加上该节点(1)就是当前节点的树高
>修改一下获取树高方法：新增意义，在获取树高的同时，判断是否平衡(即左子树或右子树树高要>0,左右两个子树的高度差的绝对值不超过 1 )，如果不平衡的话，可以返回一个负值便于判断返回结果

```js
/**
 * @param {TreeNode} root
 * @return {boolean}
 */
var isBalanced = function (root) {
    //root为null或[],返回true
    if (!root) return true;
    return getHeight(root) >= 0;
};
// 赋予递归函数一个明确的意义:先是获取树高，后面新增意义(不平衡返回负值)
const getHeight = function (node) {
    if (node === null) return 0;
    let l = getHeight(node.left);
    let r = getHeight(node.right);
    if (l < 0 || r < 0) return -2;
    if (Math.abs(l - r) > 1) return -2;
    return Math.max(l, r) + 1;
}
```
