---
title: 树的子结构
date: 2021-04-06 14:58:24
tag: Algorithm
toc: true
---

### 题目描述
输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)
B是A的子结构， 即 A中有出现和B相同的结构和节点值。
例如:
给定的树 A:
```js
     3
    / \
   4   5
  / \
 1   2
```
给定的树 B：
```js
   4 
  /
 1
```
返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

限制：
0 <= 节点个数 <= 10000

**解题思路：递归思想**

```js
/**
 * @param {TreeNode} A
 * @param {TreeNode} B
 * @return {boolean}
 */
var isSubStructure = function (A, B) {
    //判断边界问题
    //如果B为null,那么直接返回false
    if (B === null) return false;
    //B不为空，A为空时，直接返回false
    if (A === null) return false;
    //判断A的值和B的值是否相等，如果相等，且能匹配上，直接返回true
    if (A.val === B.val && is_match(A, B)) return true;
    //A和B的值不等或A,B匹配不上，那么匹配A的左子树和B或A的右子树和B
    return isSubStructure(A.left, B) || isSubStructure(A.right, B);
};
const is_match = function (a, b) {
    if (b === null) return true;
    if (a === null) return false;
    if (a.val !== b.val) return false;
    return is_match(a.left, b.left) && is_match(a.right, b.right);
}
```