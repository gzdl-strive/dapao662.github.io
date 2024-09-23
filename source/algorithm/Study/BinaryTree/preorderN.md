---
title: N叉树的前序遍历
date: 2021-04-06 11:20:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一个 N 叉树，返回其节点值的 前序遍历 。
N 叉树 在输入中按层序遍历进行序列化表示，每组子节点由空值 null 分隔（请参见示例）

**说明/提示：**
N 叉树的高度小于或等于 1000
节点总数在范围 [0, 10^4] 内

**解题思路：递归**

```js
/**
 * @param {Node} root
 * @return {number[]}
 */
 var preorder = function(root) {
    let ans = [];
    if (root === null) {
        return ans;
    }
    preorderNode(root, ans);
    return ans;
};
const preorderNode = function (node, res) {
    if (node !== null) {
        res.push(node.val);
        for (let i = 0; i < node.children.length; i++) {
            preorderNode(node.children[i], res);
        }
    }
}
```