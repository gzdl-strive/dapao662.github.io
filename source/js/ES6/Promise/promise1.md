---
title: Promise源码初步实现
date: 2020-11-01 18:43:26
tag: js
---

## 一、整体分析
>所谓`Promise`就是一个容器，有三个状态：PENDING（进行中）、FULFILLED（成功）、REJECTED（失败）,里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果，有两大特点：
* 容器状态不受外界影响
* 一旦状态改变就不会再变，任何时候都可以得到这个结果

来看下Promise的用法：
```js
new Promise((resolve, reject) => {
    //...
    //成功则执行resolve，否则执行reject
}).then(
    res => {
        //resolve对应触发函数的执行
    },
    err => {
        //reject对应触发函数的执行
    }
).then(
    //支持链式调用
    res => {

    }
).catch(
    err => console.log(err);
);
Promise.resolve();
Promise.reject();
Promise.all([promise1, promise2, ...]).then();
Promise.race([promise1, promise2, ...]).then();
```
通过用法不难分析出：
* `Promise`构造函数接受一个函数参数`exector`,`exector`接受`resolve`和`reject`两个函数并**立即执行**,通过`resolve/reject`改变状态.
* 状态改变后，触发原型链上的`then catch`方法.
* Promise类拥有静态方法`resolve、reject、all、race`.

**那么可以写出大致结构代码：**
```js
class Promise {
    constructor(exector) {
        const resolve = () => {

        }
        const reject = () => {

        }
        exector(resolve, reject);
    }
    then() {

    }
    catch() {

    }
    static resolve() {

    }
    static reject() {

    }
    static all() {

    }
    static race() {

    }
}
```
之后在此基础上补充代码.

## 二、实现初版
首先引入三种状态,完善`resolve、reject`函数,最后在构造函数内执行`exector(resolve, reject)`:

```js
//定义三种状态
const PENDING = 'PENDING'; //进行中
const FULFILLED = 'FULFILLED'; //已成功
const REJECTED = 'REJECTED'; //已失败

class Promise {
    constructor(exector) {
        //初始化状态
        this.status = PENDING;
        //将成功、失败结果放在this上,便于then、catch访问
        this.value = undefined;
        this.reason = undefined;

        const resolve = value => {
            //只有进行中状态才能更改状态
            if (this.status === PENDING) {
                this.status = FULFILLED;
                this.value = value;
            }
        }
        const reject = reason => {
            //只有进行中状态才能更改状态
            if (this.status === PENDING) {
                this.status = REJECTED;
                this.reason = reason;
            }
        }
        // 立即执行exector
        // 把内部的resolve和reject传入exector,用户可调用resolve和reject
        exector(resolve, reject);
    }
}
```
注意：`exector(resolve, reject)`;执行可能会报错，所以需要使用`try包括一下`, 有报错`reject`抛出去.
```js
class Promise {
    constructor(exector) {
        //初始化状态
        this.status = PENDING;
        //将成功、失败结果放在this上,便于then、catch访问
        this.value = undefined;
        this.reason = undefined;

        const resolve = value => {
            if (this.status === PENDING) {
                //只有进行中状态才能更改状态
                this.status = FULFILLED;
                this.value = value;
            }
        }
        const reject = reason => {
            if (this.status === PENDING) {
                //只有进行中状态才能更改状态
                this.status = REJECTED;
                this.reason = reason;
            }
        }
        //修改代码
        try {
            //立即执行exector
            //把内部的resolve和reject传入exector，用户可调用resolve和reject
            exector(resolve, reject);
        } catch(e) {
            // exector执行出错,将错误内容reject抛出去
            reject(e);
        }
    }
}
```
此时可以使用`then`进行捕获了，`then`接收两个参数，分别对应`FULFILLED`和`REJECTED`状态：
```js
new Promise().then(
    res => {},
    err => {},
)
```
注意：`then、catch`是微任务,这里使用`setTimeout`模拟：
```js
then(onFulfilled, onRejected) {
    //then微任务，这里用setTimeout模拟
    setTimeout(() => {
        if (this.status === FULFILLED) {
            //FULFILLED状态下才执行
            onFulfilled(this.value);
        } else if (this.status === REJECTED) {
            //REJECTED状态下才执行
            onRejected(this.reason);
        }
    })
}
```
Ok,出版已经完成：
```js
//定义三种状态
const PENDING = 'PENDING'; //进行中
const FULFILLED = 'FULFILLED'; //已成功
const REJECTED = 'REJECTED'; //已失败

class Promise {
    constructor(exector) {
        //初始化状态
        this.status = PENDING;
        //将成功、失败结果放在this上，便于then、catch访问
        this.value = undefined;
        this.reason = undefined;

        const resolve = value => {
            if (this.status === PENDING) {
                //只有进行中状态才能更改状态
                this.status = FULFILLED;
                this.value = value;
            }
        }
        const reject = reason => {
            if (this.status === PENDING) {
                //只有进行中状态才能更改状态
                this.status = REJECTED;
                this.reason = reason;
            }
        }
        try {
            //立即执行exector
            //把内部的resolve和reject传入exector,用户可调用resolve和reject
            exector(resolve, reject);
        } catch {
            //exector执行出错，将错误内容reject抛出去
            reject(e);
        }
    }
    then(onFulfilled, onRejected) {
        //then是微任务，这里用setTimeout模拟
        setTimeout(() => {
            if (this.status === FULFILLED) {
                //FULFILLED状态下才执行
                onFulfilled(this.value);
            } else if (this.status === REJECTED) {
                //REJECTED状态下才执行
                onRejected(this.reason);
            }
        })
    }
}
```
可以拿数据测试一下：
```js
const promise = new Promise((resolve, reject) => {
    Math.random() < 0.5 ? resolve(1) : reject(-1);
}).then(
    res => console.log(res),
    err => console.log(err),
)
```


作者：洛霞
链接：https://juejin.im/post/6860037916622913550
来源：掘金


