---
title: ES6
date: 2020-11-01 20:39:30
tag: js
---

## 四、实现catch()方法
>Promise.prototype.catch就是Promise.prototype.then(null, onRejected)的别名，所以实现就很简单了：
```js
catch(onRejected) {
  return this.then(null, onRejected);
}
```

## 五、Promise.resolve()
这里就不考虑参数是`thenable`对象了，那么参数有两种情况：
1. Promise实例
2. 不是Promise实例
```js
static resolve(value) {
  if (value instanceof Promise) {
    // 如果是Promise实例，直接返回
    return value;
  } else {
    // 如果不是Promise实例，返回一个新的Promise对象，状态为FULFILLED
    return new Promise((resolve, reject) => resolve(value));
  }
}
```

## 六、Promise.reject()
`Promise.reject`也会返回一个`Promise`实例，状态为`REJECTED`
与`Promise.resolve`不同的是，`Promise.reject`方法的参数会原封不动地作为`reject`的参数
```js
static reject(reason) {
  return new Promise((resolve, reject) => {
    reject(reason);
  })
}
```

## 七、Promise.all()
返回一个`promise`对象，只有当所有`promise`都成功时返回的`promise`状态才成功，需要注意的点是：
1. 所有的promise状态变为FULFILLED，返回的promise状态才变为FULFILLED。
2. 一个promise状态变为REJECTED，返回的promise状态就变为REJECTED。
3. 数组成员不一定都是promise，需要使用Promise.resolve()处理。

```js
static all(promiseArr) {
    const len = promiseArr.length;
    const values = new Array(len);
    // 记录已经成功执行的promise个数
    let count = 0;
    return new Promise((resolve, reject) => {
        for (let i = 0; i < len; i++) {
            // Promise.resolve()处理，确保每一个都是promise实例
            Promise.resolve(promiseArr[i]).then(
                val => {
                    values[i] = val;
                    count++;
                    // 如果全部执行完，返回promise的状态就可以改变了
                    if(count === len) {
                        resolve(values);
                    }
                },
                err => reject(err),
            );
        }
    })
}
```

## 八、Promise.race()
`Promise.race()`实现就比较简单了：
```js
static race(promiseArr) {
    return new Promise((resolve, reject) => {
        promiseArr.forEach(p => {
            Promise.resolve(p).then(
                val => resolve(val),
                err => reject(err),
            )
        })
    })
}
```
