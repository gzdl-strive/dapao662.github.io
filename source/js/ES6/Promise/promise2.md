---
title: Promise支持异步和链式调用
date: 2020-11-01 19:50:55
tag: js
---

## 三、至此异步和链式调用
    此时初版还有三个方向需要完善：
    1. Promise内部异步代码执行的问题。
    2. Promise的链式调用
    3. 值传透

### 支持异步代码
开发中经常会将接口放于`promise`内部，等接口请求响应成功把数据`resolve`出去，或失败时把数据`reject`出去，此时`then、catch`才会进行捕获.

而现在的代码，`promise`内部如果有异步代码执行后才`resolve`，`then`不会等待异步代码执行完毕会立即执行，所以此时状态是PENDING，不会触发`then`的回调函数.

新增**onFulfilledCallbacks、onRejectedCallbacks**维护成功态、失败态任务队列:
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

        //新增代码:
        //成功态回调函数队列
        this.onFulfilledCallbacks = [];
        //失败态回调函数队列
        this.onRejectedCallbacks = [];

        const resolve = value => {
            if (this.status === PENDING) {
                //只有进行中状态才能更改状态
                this.status = FULFILLED;
                this.value = value;

                //新增代码：
                //成功态函数依次执行
                this.onFulfilledCallbacks.forEach(fn => fn(this.value));
            }
        }
        const reject = reason => {
            if (this.status === PENDING) {
                //只有进行中状态才能更改状态
                this.status = REJECTED;
                this.reason = reason;

                //新增代码：
                //失败态函数依次执行
                this.onRejectedCallbacks.forEach(fn => fn(this.reason));
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
            //新增代码：
            if(this.status === PENDING) {
                //状态是PENDING下执行
                //说明promise内部有异步代码执行，还未改变状态，添加到成功/失败任务回调队列即可
                this.onFulfilledCallbacks.push(onFulfilled);
                this.onRejectedCallbacks.push(onRejected);
            } else if (this.status === FULFILLED) {
                //FULFILLED状态下才执行
                onFulfilled(this.value);
            } else if (this.status === REJECTED) {
                //REJECTED状态下才执行
                onRejected(this.reason);
            }
        })
    }
}

const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve(1), 1000);
}).then(
  res => console.log(res)
)
// 1
```

### 实现链式调用
`Promise`的一大优势就是支持链式调用,具体来说就是`then`方法的具体实现，实际上是返回了一个`Promise`，需要注意的几个点：
1. 保存之前的`promise`实例的引用，即保存`this`
2. 根据`then`回调函数执行的返回值
* 如果是promise实例，那么返回的下一个promise实例会等待这个promise状态发生变化
* 如果不是promise实例，根据目前情况直接执行`resolve`或`reject`

**完善`then`函数**:
```js
then(onFulfilled, onRejected) {
    //保存this
    const self = this;
    return new Promise((resolve, reject) => {
        if (self.status === PENDING) {
            self.onFulfilledCallbacks.push(() => {
                //try捕获错误
                try {
                    //模拟微任务
                    setTimeout(() => {
                        const result = onFulfilled(self.value);
                        //分两种情况：
                        //1、回调函数返回值是Promise,执行then操作
                        //2、如果不是Promise,调用新Promise的resolve函数
                        result instanceof Promise ? result.then(resolve, reject) : resolve(result);
                    }) 
                } catch(e) {
                  reject(e);
                }
            });
            self.onRejectedCallbacks.push(() => {
                //以下同理
                try {
                    setTimeout(() => {
                        const result = onRejected(self.reason);
                        result instanceof Promise ? result.then(resolve, reject) : reject(result);
                    })
                } catch(e) {
                    reject(e);
                }
            })
        } else if (self.status === FULFILLED) {
            setTimeout(() => {
                try {
                    const result = onFulfilled(self.value);
                    result instanceof Promise ? result.then(resolve, reject) : resolve(result);
                } catch(e) {
                    reject(e);
                }
            })
        } else if (self.status === REJECTED) {
            setTimeout = (() => {
                try {
                    const result = onRejected(self.reason);
                    result instanceof Promise ? result.then(resolve, reject) : resolve(result);
                } catch(e) {
                    reject(e);
                }
            })
        }
    })
}

```


### 值传透
**`Promise`支持值穿透**
```js
let promise = new Promise((resolve, reject) => {
    resolve(1);
}).then(2).then(3).then(value => {
    console.log(value);
})
//1
```
>`then`参数期望是函数，传入非函数则会发生值穿透。值传透可以理解为，当传入`then`的不是函数的时候，这个`then`是无效的。

原理上是当then中传入的不算函数，则这个promise返回上一个promise的值，这就是发生值穿透的原因，所以只需要对then的两个参数进行设置就行了：
```js
onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
onRejected = typeof onRejected === 'function'? onRejected:
    reason => { throw new Error(reason instanceof Error ? reason.message:reason) }
```
完整的`then`函数代码：
```js
then(onFulfilled, onRejected) {
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
  onRejected = typeof onRejected === 'function'? onRejected:
    reason => { throw new Error(reason instanceof Error ? reason.message:reason) }
  // 保存this
  const self = this;
  return new Promise((resolve, reject) => {
    if (self.status === PENDING) {
      self.onFulfilledCallbacks.push(() => {
        // try捕获错误
        try {
          // 模拟微任务
          setTimeout(() => {
            const result = onFulfilled(self.value);
            // 分两种情况：
            // 1. 回调函数返回值是Promise，执行then操作
            // 2. 如果不是Promise，调用新Promise的resolve函数
            result instanceof Promise ? result.then(resolve, reject) : resolve(result);
          })
        } catch(e) {
          reject(e);
        }
      });
      self.onRejectedCallbacks.push(() => {
        // 以下同理
        try {
          setTimeout(() => {
            const result = onRejected(self.reason);
            // 不同点：此时是reject
            result instanceof Promise ? result.then(resolve, reject) : reject(result);
          })
        } catch(e) {
          reject(e);
        }
      })
    } else if (self.status === FULFILLED) {
      try {
        setTimeout(() => {
          const result = onFulfilled(self.value);
          result instanceof Promise ? result.then(resolve, reject) : resolve(result);
        });
      } catch(e) {
        reject(e);
      }
    } else if (self.status === REJECTED){
      try {
        setTimeout(() => {
          const result = onRejected(self.reason);
          result instanceof Promise ? result.then(resolve, reject) : resolve(result);
        })
      } catch(e) {
        reject(e);
      }
    }
  });
}
```









作者：洛霞
链接：https://juejin.im/post/6860037916622913550
来源：掘金
