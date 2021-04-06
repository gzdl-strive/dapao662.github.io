---
title: 二叉树的后序遍历
date: 2021-03-31 10:07:24
tag: Algorithm
toc: true
---

### 题目描述
>给定一个二叉树，返回它的 后序 遍历。

**示例:**
输入: [1,null,2,3]  
```js
   1
    \
     2
    /
   3 
```
输出: [3,2,1]
进阶: 递归算法很简单，你可以通过迭代算法完成吗？

### 解法一、递归
```js
const postorderTraversal = function (root) {
    let result = [];
    recursion(root, result);
    return result;
}
const recursion = function (node, res) {
    if (node !== null) {
        recursion(node.left, res);
        recursion(node.right, res);
        res.push(node.val);
    }
}
```

### 解法二、非递归（迭代）
>技巧是使用两个栈，一个数据栈，一个状态栈。将“遍历左子树”，“遍历右子树”和“访问根节点”三个步骤分别用状态码表示，枚举状态转移过程，使用有限状态自动机(FSM,Finite State Machine)的模型来模拟递归过程.数据栈用来存放当前遍历的节点，状态栈用来存放要访问哪个方向(0左，1右，2根)
```js
const postorderTraversal = function (root) {
    if (root === null) {
        return [];
    }
    const s1 = [];//数据栈:递归过程中的局部变量
    const s2 = [];//状态栈:递归到的程序位置
    const res = [];//返回值数组
    s1.push(root);//从根节点开始遍历
    s2.push(0);//从左子树开始
    while (s1.length) {
        //循环结束条件为s1为空，即s1数组中已经没有了要处理的节点
        let status = s2.pop();//使用一个status状态存放s2栈顶的状态==》相当于let status = s2[s2.length - 1]; s2.pop();
        //如果是遍历的左子树
        if (status === 0) {
            //现在遍历的是左子树，下一次需要遍历右子树，所以需要把1状态加入到s2栈中
            s2.push(1);
            //判断左子树为不为空
            if (s1[s1.length - 1].left !== null) {
                //左子树不为空，则加入到要处理的数据栈栈顶
                s1.push(s1[s1.length - 1].left);
                //接着处理这个节点的左子树
                s2.push(0);
            }
        } else if (status === 1) {
            //现在遍历的是右子树，下一次需要遍历当前子树的根节点,所以需要把2状态加入到s2栈中
            s2.push(2);
            //判断右子树是否为空
            if (s1[s1.length - 1].right !== null) {
                //右子树不为空，则加入到要处理的数据栈栈顶
                s1.push(s1[s1.length - 1].right);
                //接着处理这个节点的左子树
                s2.push(0);
            }
        } else {
            //现在需要遍历的是根节点
            //直接把当前处理的节点的值加入到返回值数组中
            res.push(s1[s1.length - 1].val);
            //当前节点处理完成了，弹出栈
            s1.pop();
        }
    }
    return res;
}
```