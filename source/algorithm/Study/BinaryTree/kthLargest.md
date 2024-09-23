---
title: 二叉搜索树的第k大结点
date: 2021-04-06 14:44:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一棵二叉搜索树，请找出其中第k大的节点。

**示例**
示例 1:
输入: root = [3,1,4,null,2], k = 1
```js
   3
  / \
 1   4
  \
   2
```
输出: 4

示例 2:
输入: root = [5,3,6,2,4,null,null,1], k = 3
```js
       5
      / \
     3   6
    / \
   2   4
  /
 1
```
输出: 4

提示：
1 ≤ k ≤ 二叉搜索树元素个数

### 解题思路
**方法一：二叉搜索树，根据判断左右子树的节点数量，判断第k大节点在哪**
* 如果右子树的节点数量(cnt_R) >= k,那么第k大的节点在右子树
* 如果k = cntR + 1 那么，该节点在根节点
* 如果k > cnt_R + 1的话，那么该节点在左子树

**方法二：二叉搜索树，如果按照中序遍历的话，那么得到的序列将会是有序的，那么，我们就可以先得到一个有序的序列，然后得出第k大的节点**

### 方法一：判断右子树节点数量
```js
/**
 * @param {TreeNode} root
 * @param {number} k
 * @return {number}
 */
const kthLargest = function (root, k) {
    if (root === null) return null;
    //获取右子树的节点数量
    let cntR = getCount(root.right);
    //如果右子树节点数量 >= k,那么第k大节点就在右子树中
    if (cntR >= k) {
        return kthLargest(root.right, k);
    } else if (cntR + 1 === k) {
        //如果k = 右子树节点数量 + 1，那么第k大节点就是根节点
        return root.val;
    } else {
        //如果k > 右子树节点数量 + 1，那么第K大节点就在左子树中，递归的时候(右边的我们以及判断完了)，我们要找左子树的k - cntR - 1大的节点=》和找第k大意义相同
        return kthLargest(root.left, k - cntR - 1);
    }
}
const getCount = function (node) {
    if (!node) return 0;
    return getCount(node.left) + getCount(node.right) + 1;
}
```

### 方法二：中序遍历
```js
/**
 * @param {TreeNode} root
 * @param {number} k
 * @return {number}
 */
var kthLargest = function(root, k) {
    if (root === null) return null;
    let ans = [];
    //中序遍历的到有序序列
    kthLargestArr(root, ans);
    //返回第k大的值,例如：第1大，那么返回序列最后一个，即ans[ans.length - 1]
    return ans[ans.length - k];
};
const kthLargestArr = function (node, ans) {
    if (node !== null) {
        kthLargestArr(node.left, ans);
        ans.push(node.val);
        kthLargestArr(node.right, ans);
    }
}
```