---
title: 从前序与中序遍历序列构造二叉树
date: 2021-04-06 14:44:24
tag: Algorithm
toc: true
---

### 题目描述
>根据一棵树的前序遍历与中序遍历构造二叉树。
注意:
你可以假设树中没有重复的元素。
例如，给出
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：
```js
    3
   / \
  9  20
    /  \
   15   7
```

### 解题思路：
前序遍历：根，左子树，右子树
中序遍历：左子树，根，右子树
=>所以前序遍历的第一个节点就是根节点=》根据这个节点可以找到该节点在中序遍历中的位置，那么它的左边是它的左子树，右边是右子树
那么我们就能获取到左子树的中序遍历和左子树的前序遍历=》递归
递归函数的意义：找到该节点，并找到它的左右子树

```js
/**
 * @param {number[]} preorder
 * @param {number[]} inorder
 * @return {TreeNode}
 */
var buildTree = function(preorder, inorder) {
    if (preorder.length === 0) return null;
    let pos = 0;
    while (inorder[pos] !== preorder[0]) pos++;
    let l_pre = [], l_in = [], r_pre = [], r_in = [];
    for (let i = 0; i < pos; i++) {
        l_pre.push(preorder[i + 1]);
        l_in.push(inorder[i]);
    }
    for (let i = pos + 1; i < preorder.length; i++) {
        r_pre.push(preorder[i]);
        r_in.push(inorder[i]);
    }
    let node = new TreeNode(preorder[0]);
    node.left = buildTree(l_pre, l_in);
    node.right = buildTree(r_pre, r_in);
    return node;
};
```