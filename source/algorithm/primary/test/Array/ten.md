---
title: 旋转图像
date: 2021-03-02 16:17:24
tag: Algorithm
---

### 旋转图像

> 给定一个 n × n 的二维矩阵 matrix 表示一个图像。

请你将图像顺时针旋转 90 度。
你必须在 原地 旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要 使用另一个矩阵来旋转图像。

**示例**
输入：matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
输出：[[7, 4, 1], [8, 5, 2], [9, 6, 3]]
输入：matrix = [[5, 1, 9, 11], [2, 4, 8, 10], [13, 3, 6, 7], [15, 14, 12, 16]]
输出：[[15, 13, 2, 5], [14, 3, 4, 1], [12, 6, 8, 9], [16, 7, 10, 11]]

### 方法一、使用辅助数组

``` js
/*
5 1 9 11        x x x 5
x x x x    =>   x x x 1 
x x x x         x x x 9
x x x x         x x x 11
第一行的第x个元素在旋转后出现在倒数第一列的第x个元素
x x x x         x  x 2 x
2 4 8 10  =>    x  x 4 x
x x x x         x  x 8 x
x x x x         x  x 10 x
*/
```

第二行的第x个元素在旋转后出现在倒数第二列的第x个元素
对于矩阵中的第三行和第四行同理。这样我们可以得到规律：

    **对于矩阵中第i行的第j个元素，在旋转后，它会出现在倒数第i列的第j个位置**

将其翻译成代码： ` matrix[row][col] ` => 旋转后 => ` matrixNew[col][n - 1 - row]`

这样以来，我们使用一个与matrix 大小相同的辅助数组matrixnew，临时存储旋转后的结果。
我们遍历matrix 中的每一个元素，根据上述规则将该元素存放到matrixnew中对应的位置。
在遍历完成之后，再将matrixnew中的结果复制到原数组中即可。

``` js
const rotate = function(matrix) {
    const n = matrix.length;
    const matrixNew = new Array(n).fill(0).map(() => new Array(n).fill(0));
    for (let i = 0; i < n; i++) {
        for (let j = 0; j < n; j++) {
            matrixNew[j][n - i - 1] = matrix[i][j];
        }
    }
    for (let i = 0; i < n; i++) {
        for (let j = 0; j < n; j++) {
            matrix[i][j] = matrixNew[i][j];
        }
    }
    return matrix;
}
let matrix = [
    [5, 1, 9, 11],
    [2, 4, 8, 10],
    [13, 3, 6, 7],
    [15, 14, 12, 16]
];
console.log(matrix);
console.log(rotate(matrix));
/*
[
  [ 5, 1, 9, 11 ],
  [ 2, 4, 8, 10 ],
  [ 13, 3, 6, 7 ],
  [ 15, 14, 12, 16 ]
]
[
  [ 15, 13, 2, 5 ],
  [ 14, 3, 4, 1 ],
  [ 12, 6, 8, 9 ],
  [ 16, 7, 10, 11 ]
]
*/
```

**复杂度分析**

> 1、时间复杂度：O(N^2), 其中N是matrix的边长

2、空间复杂度：O(N^2)，我们需要使用一个和matrix大小相同的辅助数组

### 方法二：原地旋转

题目中要求我们尝试在不使用额外内存空间的情况下进行矩阵的旋转，也就是说，我们需要「原地旋转」这个矩阵。那么我们如何在方法一的基础上完成原地旋转呢？
我们观察方法一中的关键等式：
 `matrixnew[col][n−row−1]=matrix[row][col]`

它阻止了我们进行原地旋转，这是因为如果我们直接将 `matrix[row][col]` 放到原矩阵中的目标位置 `matrix[col][n−row−1]` ： `matrix[col][n−row−1]=matrix[row][col]`

原矩阵中的 `matrix[col][n−row−1]` 就被覆盖了！这并不是我们想要的结果; 
因此我们可以考虑用一个临时变量 `temp` 暂存 `matrix[col][n−row−1]` 的值
这样虽然 `matrix[col][n−row−1]` 被覆盖了，我们还是可以通过 temp 获取它原来的值：

    {
        temp                 = matrix[col][n-row-1]
        matrix[col][n-row-1] = matrix[row][col]
    }

那么 `matrix[col][n-row-1]` 经过旋转操作之后会到哪个位置呢?
我们还是使用方法一中的等式，这次，我们需要将

    {
        row = col
        col = n - row - 1
    }

带入关键等式，就可以得到

 `martrix[n-row-1][n-col-1] = matrix[col][n-row-1];`

同样的，直接复制会覆盖掉matrix[n-row-1][n-col-1]原来的值，因此我们还是需要使用temp存储, 不过这次，我们可以直接使用之前的临时变量temp:

    {
        temp = matrix[n-row-1][n-col-1]
        matrix[n-row-1][n-col-1] = matrix[col][n-row-1]
        matrix[col][n-row-1] = matrix[row][col]
    }

我们再重复一次之前的操作， `matrix[n−row−1][n−col−1]` 经过旋转操作之后会到哪个位置呢？

    {
       row = n-row-1
       col = n-col-1 
    }

带入关键等式，就可以得到

 `matrix[n-col-1][row] = matrix[n-row-1][n-col-1]`

写入 

    {
        temp = matrix[n-col-1][row]
        matrix[n-col-1][row] = matrix[n-row-1][n-col-1]
        matrix[n-row-1][n-col-1] = matrix[col][n-row-1]
        matrix[col][n-row-1] = matrix[row][col]
    }

再来一次, 

    {
        row = n-col -1
        col = row
    }

带入关键等式，就可以得到

 `matrix[row][col] = matrix[n-col-1][row]`

我们回到了最初的起点matrix[row][col]，也就是说

    {   
        matrix[row][col] = matrix[n-col-1][row]
        matrix[n-col-1][row] = matrix[n-row-1][n-col-1]
        matrix[n-row-1][n-col-1] = matrix[col][n-row-1]
        matrix[col][n-row-1] = matrix[row][col]
    }

这四项处于一个循环中, 并且每一项旋转后的位置就是下一项所在的位置！
因此我们可以使用一个临时变量 temp 完成这四项的原地交换：

``` js
    {
        temp = matrix[row][col]
        matrix[row][col] = matrix[n - col - 1][row]
        matrix[n - col - 1][row] = matrix[n - row - 1][n - col - 1]
        matrix[n - row - 1][n - col - 1] = matrix[col][n - row - 1]
        matrix[col][n - row - 1] = temp
    }
```

当我们知道了如何原地旋转矩阵之后，还有一个重要的问题在于：**我们应该枚举哪些位置 (row, col) 进行上述的原地交换操作呢？**由于每一次原地交换四个位置，因此：

> 当 n 为偶数时，我们需要枚举 n^2 / 4 = (n/2)x(n/2)个位置，可以将该图形分为四块，以4×4的矩阵为例：

保证了不重复、不遗漏；
当 n 为奇数时，由于中心的位置经过旋转后位置不变，我们需要枚举(n^2)-1 /4 = ((n-1)/2)x((n+1)/2)个位置
同样保证了不重复、不遗漏，矩阵正中央的点无需旋转。

``` js
const rotate2 = function(matrix) {
    const n = matrix.length;
    // 枚举次数： 当n为偶数时 n/2 x n/2 例如：4 = 2 * 2  当n为奇数时 (n-1)/2 (n+1)/2 例如：5 = 2 * 3 
    for (let i = 0; i < Math.floor(n / 2); i++) {
        for (let j = 0; j < Math.floor((n + 1) / 2); j++) {
            /*
            temp = matrix[row][col]
            matrix[row][col] = matrix[n-col-1][row]
            matrix[n-col-1][row] = matrix[n-row-1][n-col-1]
            matrix[n-row-1][n-col-1] = matrix[col][n-row-1]
            matrix[col][n-row-1] = temp
            */
            const temp = matrix[i][j];
            matrix[i][j] = matrix[n - j - 1][i];
            matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
            matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
            matrix[j][n - i - 1] = temp;
        }
    }
    return matrix;
}
let matrix2 = [
    [5, 1, 9, 11],
    [2, 4, 8, 10],
    [13, 3, 6, 7],
    [15, 14, 12, 16]
];
console.log(matrix2);
console.log(rotate2(matrix2));
```

**复杂度分析**

> 1、时间复杂度：O(N^2), 其中N是matrix的边长。我们需要枚举的子矩阵大小为O(n/2) x O((n+1)/2) = O(N^2)

2、空间复杂度：O(1)，为原地旋转

### 方法三：使用翻转代替旋转

我们还可以另辟蹊径，用翻转代替旋转操作。以示例二为例

``` js
/*
 5  1  9  11
 2  4  8  10
 13 3  6  7
 15 14 12 16
先将其按水平轴翻转得到:
 15 14 12 16
 13  3  6  7
 2  4  8  10
 5  1  9  11
再根据主对角线翻转得到：
 15 13 2  5
 14 3  4  1
 12 6  8  9
 16 7  10 11
*/
```

就得到了答案。这是为什么呢？对于水平轴翻转而言，我们只需要枚举矩阵上半部分的元素，和下半部分的元素进行交换，即
 `matrix[row][col] ==> (水平轴翻转) ==> matrix[n-row-1][col]`

对于主对角线翻转而言，我们只需要枚举对角线左侧的元素，和右侧的元素进行交换，即
 ` matrix[row][col] ==> (主对角线翻转) ==> matrix[col][row]`

将它们联立即可得到：

 `matrix[row][col] ==> (水平轴翻转) ==> matrix[n-row-1][col] ==> (主对角线翻转) ==> matrix[col][n-row-1]`

和方法一、方法二中的关键等式
`martixNew[col][n-row-1] = matrix[row][col]` 是一致的

``` js
const rotate3 = function(matrix) {
    const n = matrix.length;
    // 水平翻转
    for (let i = 0; i < Math.floor(n / 2); i++) {
        for (let j = 0; j < n; j++) {
            [matrix[i][j], matrix[n - i - 1][j]] = [matrix[n - i - 1][j], matrix[i][j]]
        }
    }
    // 主对角线翻转
    for (let i = 0; i < n; i++) {
        for (let j = 0; j < i; j++) {
            [matrix[i][j], matrix[j][i]] = [matrix[j][i], matrix[i][j]];
        }
    }
    return matrix;
}
let matrix3 = [[5, 1, 9, 11], [2, 4, 8, 10], [13, 3, 6, 7], [15, 14, 12, 16]];
console.log('matrix3:', rotate3(matrix3));
```
**复杂度分析**
>1、时间复杂度：O(N^2),其中N是matrix的边长。对于每一次翻转操作，我们都需要枚举矩阵中一半的元素
2、空间复杂度：O(1)，为原地翻转得到的原地旋转
