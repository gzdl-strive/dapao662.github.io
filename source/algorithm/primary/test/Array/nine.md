---
title: 有效的数独
date: 2021-03-02 15:59:24
tag: Algorithm
---

### 有效的数独
判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。
(1)数字 1-9 在每一行只能出现一次。
(2)数字 1-9 在每一列只能出现一次。
(3)数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。

### 三个步骤清晰解题
#### 步骤一,设置三元素
1. rows: 存放1-9横排的值
2. columns：存放1-9纵排的值
3. boxes：存放1-9小盒子的值

#### 步骤二，顺序遍历存放值
1. 通过双重for遍历
2. rows[i]存放横坐标的值
3. columns[j]存放对于纵坐标的值
4. boxes[?]存放对于小盒子的值
那么小盒子的值怎么判断呢? 要怎么填充？

#### 步骤三，确定问号
借用以下leetCode官方图
![1](/assets/AlgorithmImg/shudu.png "盒子")

```js
const isValidSudoku = function (board) {
    // 三个方向判重
    let rows = {};
    let columns = {};
    let boxes = {};
    // 遍历数独
    for (let i = 0; i < 9; i++) {
        for (let j = 0; j < 9; j++) {
            let num = board[i][j];
            if (num !== '.') {
                // 子数独序号
                let boxIndex = parseInt(i / 3) * 3 + parseInt(j / 3);
                if (rows[i + '-'+ num] || columns[j + '-' + num] || boxes[boxIndex + '-' + num]) {
                    return false;
                }
            }
            // 以各自方向 + 不能出现重复的数字 组成唯一键值，若出现第二次，即为重复
            rows[i + '-'+ num] = true;
            columns[j + '-' + num] = true;
            boxes[boxIndex + '-' + num] = true;
        }
    }
    return true;
}
```