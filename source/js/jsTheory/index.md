---
title: js理论
date: 2021-01-28 09:39:24
toc: true
tag: js
---

## js运行时,编译时

### js程序数据状态 => 内存

#### 分配
>存放声明的数据、获取使用的数据 ->堆内存 栈内存
* 堆内存: 数据大小不可控的、不知道什么时候被访问了
* 栈内存: 存储静态类型数据(boolean,null,number...),对象和函数的引用地址--->存的是基本数据类型,还有函数执行时分配的内存(执行栈的说法)
```js
const num = 4;
const name = 'abc';
const isNB = true;
const obj = {
    count: 4
}
function a() {
    let name = '222';
    console.log(name);
}
a();
```
|       count: 4            | (存放堆数据)低位
|           函数a           |   
--------------------        | 
|  (栈清空但是堆内存没有清空->堆内存不知道栈已经清空了,是垃圾回收引擎（gc）知道栈清空了(了)) 
|  当前函数执行完成，出栈相关数据
|       ...执行完毕（出栈）  |          
|       name入栈（出栈）     |     
|       a的执行入栈  （出栈）|       
|      a的引用地址 （出栈）  |          
|      obj的引用地址  （出栈 |              
|      isNB=true （出栈）   |        
|      name="abc"  （出栈） |          
|       num=4    （出栈）   | (存放栈数据)高位
**js单线程，垃圾回收影响吗?** ->影响的
>垃圾回收处理的是堆数据

堆里面的数据:
* 新生代:新创建
新生代数据=>存活少 Scavenge
>划分两段(一段是From状态:使用状态)==>(达到条件,From存不下数据的时候,触发垃圾回收)遍历From的数据,把存活的数据拷贝到TO状态,并标记(然后执行回收把失活的数据清除(from))  =>(另一段是to状态：闲置状态) => 这时候因为TO状态是拷贝的数据就转变成了From状态，而From状态因为被清除了就变成To（闲置）状态

新->老（晋升条件）: 1、标记 2、存活
老生代垃圾回收触发条件： 新生代晋升老生代-》内存没了，触发老生代垃圾回收
* 老生代:上一次扫描没有被清理掉-》不容易被回收(不断晋升)
存活多-》不能用拷贝的方法-->删死数据
两个算法：标记清除、标记整理
标记清除(mark sweep)：
1. 遍历 存活 =》 GC 时间长 => 浏览器卡顿
2. 删除没有被使用的数据(少数)
数据段 不连续 -> 还有内存 分配不了大数据 =>(不会触发垃圾回收，没有意义)=>标记整理 移动整合内存段 多 成本大(GC 时间长 => 浏览器卡顿) => 增量标记(增量整理,只处理一部分)


## 浏览器架构
### 渲染进程
>一个tab相对于一个渲染进程

一个进程：
* Compositor Thread 排版线程 接收用户事件的输入(如果没有绑定事件的js不会经过主线程会直接提交给GPU)
* Main Thread 主线程 处理js执行、html,css解析、排版
* Compositor Title 栅格化

一帧：
Frame Start ->
    `Main Thread(主线程)`: Input event handlers -> requestAnimationFrame -> Parse HTML(解析HTML) -> Recaic(重新计算) Styles -> Layout(Layout树) -> Update Layer Tree -> Paint(控制层级) -> Composite -> commit -> Raster Scheduled -> Rasterize 
-> Frame End 
    requestIdleCallback

### GPU进程
GPU负责渲染


### 杂谈
parseHTML时 遇到js会阻塞 => 执行完js完回来继续执行
setTimeout => 是浏览器提供的监听timer
事件循环 独立于 js执行(堆,栈...)

### nodejs v8 libuv
1. v8不负责事件循环 setTimeout
2. node维护事件 处理逻辑 是一个队列吗?? ->多个队列 多个优先级
3. 事件循环和js执行在同一个线程吗? 是在同一个线程
4. 事件循环的先后顺序