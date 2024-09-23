---
title: 击鼓传花
date: 2021-02-25 17:18:24
tag: Algorithm
---

### 游戏规则
>原游戏规则：
    班级中玩一个游戏，所有学生围成一圈，从某位同学手里开始向旁边的同学传递花
    这个时候某个人（比如班长），在击鼓，鼓声停下的时候，花在谁手里，谁就出来表演节目
修改游戏规则：
    几个朋友围成一圈，开始数数，数到某个数字的人自动淘汰。
    最后剩下的这个人获得胜利,请问最后剩下的是原来在哪一个位置上的人？

### 函数

#### 队列类
```js
function Queue() {
    //属性
    this.items = [];
    //方法
    // 1、将元素添加到队列中
    Queue.prototype.enqueue = function (element) {
        this.items.push(element);
    }
    // 2、从队列中移除前端元素-->使用数组性能不高，因为删除一个元素，全部都要移动
    Queue.prototype.dequeue = function () {
        return this.items.shift();
    }
    // 3、查看前端的元素
    Queue.prototype.front = function () {
        return this.items[0];
    }
    // 4、查看队列是否为空
    Queue.prototype.isEmpty = function () {
        return this.items.length === 0;
    }
    // 5、查看队列中元素的个数
    Queue.prototype.size = function () {
        return this.items.length;
    }
    // 6、toString方法
    Queue.prototype.toString = function () {
        return this.items.join(' ');
    }
}
```

#### 函数实现
```js
function passGame(nameList, num) {
    // 1、常见一个队列结构
    const queue = new Queue();
    // 2、将所有人依次加入到队列中
    for (let i = 0; i < nameList.length; i++) {
        queue.enqueue(nameList[i]);
    }
    // 3、开始数数，不是Num的时候，重新加入到队列末尾,是num时，删除它
    while (queue.size() > 1) {
        // 3.1-num数字之前的人重新放入到队列末尾
        for (let i = 0; i < num - 1; i++) {
            queue.enqueue(queue.dequeue());
        }
        // 3.2-num数字对于这个人，直接删除掉
        queue.dequeue();
    }
    // 4、获取剩下的人
    const endName = queue.front();
    console.log(`剩下的人为: ${endName}`);
    return nameList.indexOf(endName);
}
const names = ['关羽','刘备','张飞','赵云','诸葛亮','曹操'];
console.log(passGame(names, 2));
//剩下的人为：诸葛亮
// 4
```