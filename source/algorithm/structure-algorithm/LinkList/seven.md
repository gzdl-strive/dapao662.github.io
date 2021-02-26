---
title: 双向链表的常见操作
date: 2021-02-26 10:14:24
tag: Algorithm
---

### 双向链表的常见操作
### 增
append(element): 向列表尾部添加一个新的项
insert(position, element): 项列表的特定位置插入一个新的项
### 删
removeAt(position): 从列表的特定位置移除一项.
reomve(element): 从列表中移除一项
### 改
update(position, element):修改某个位置的元素
### 查
get(position): 获取对应位置的元素
indexOf(element): 返回元素在列表中的索引。如果列表中没有改元素则返回-1
### 其他
isEmpty(): 如果链表不包含任何元素，返回true,如果链表长度大于0则返回false;
size(): 返回链表包含的元素个数，与数组的长度属性类似
toString(): 由于列表项使用了Node类，就需要重写继承自javascript对象默认的toString方法，让其只输出元素的值
forwardString(): 返回正向遍历的节点字符串形式
backwardString(): 返回反向遍历的节点字符串形式