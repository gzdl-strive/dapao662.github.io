---
title: Class-TS版本Promise封装
date: 2022-07-28 10:41:24
toc: true
---

## 类型
```ts
export type PromiseType = any;
export type PromiseState = 'pending' | 'fullfilled' | 'rejected';
export type Callback = {
  onResolved: () => void;
  onRejected: () => void;
}
```

## 实现
```ts
import { PromiseType, PromiseState, Callback } from './types/typing';

class MyPromise {
  PromiseState: PromiseState;
  PromiseResult: PromiseType;
  callbacks: Callback[];
  constructor(executor: (...args: any[]) => void) {
    this.PromiseState = 'pending'; // Promise对象的状态
    this.PromiseResult = null; // Promise对象的值
    this.callbacks = []; // 用于保存异步回调的队列
    const self = this; // 保存this指向

    function resolve(value: PromiseType) {
      // Promise的状态只能修改一次
      if (self.PromiseState !== 'pending') return;
      self.PromiseState = 'fullfilled'; // 修改Promise的状态
      self.PromiseResult = value; // 修改值
      self.callbacks.length && self.callbacks.map(item => {
        item.onResolved();
      })
    }
    function reject(reason: PromiseType) {
      if (self.PromiseState !== 'pending') return;
      self.PromiseState = 'rejected';
      self.PromiseResult = reason;
      self.callbacks.length && self.callbacks.map(item => {
        item.onRejected();
      })
    }

    // 使用try catch取捕获throw抛出的信息
    try {
      // 同步调用
      executor(resolve, reject);
    } catch (e) {
      reject(e);
    }
  }

  then(onResolved?: (...args: unknown[]) => unknown, onRejected?: (...args: unknown[]) => unknown): MyPromise {
    const self = this; // 保存this指向
    // 异常穿透
    if (typeof onRejected !== 'function') {
      onRejected = reason => {
        throw reason;
      }
    }
    // 值传递
    if (typeof onResolved !== 'function') {
      onResolved = value => value;
    }

    // then方法返回要给Promise对象
    return new MyPromise((resolve, reject) => {
      function callback(type: (...args: unknown[]) => unknown) {
        try {
          let result = type(self.PromiseResult);
          if (result instanceof MyPromise) {
            result.then(
              v => resolve(v),
              r => reject(r)
            );
          } else {
            resolve(result);
          }
        } catch (e) {
          reject(e);
        }
      }
      // 同步-成功的状态
      if (self.PromiseState === 'fullfilled') {
        callback(onResolved!);
      }
      // 同步-失败的状态
      if (self.PromiseState === 'rejected') {
        callback(onRejected!);
      }
      // 异步-状态还没修改
      if (self.PromiseState === 'pending') {
        self.callbacks.push({
          onResolved: function () {
            callback(onResolved!);
          },
          onRejected: function () {
            callback(onRejected!);
          }
        })
      }
    });
  }

  catch(onRejected: (...args: unknown[]) => unknown) {
    return this.then(undefined, onRejected);
  }

  static resolve(value: PromiseType) {
    return new MyPromise((resolve, reject) => {
      if (value instanceof MyPromise) {
        value.then(
          v => resolve(value),
          r => reject(r)
        );
      } else {
        resolve(value);
      }
    })
  }

  static reject(reason: PromiseType) {
    return new MyPromise((resolve, reject) => {
      reject(reason);
    })
  }

  static all(promises: MyPromise[]) {
    return new MyPromise((resolve, reject) => {
      let count: number = 0;
      const arr: PromiseType[] = [];
      for (let i = 0; i < promises.length; i++) {
        promises[i].then(
          v => {
            count++;
            arr.push(v);
            if (count === promises.length) {
              resolve(arr);
            }
          },
          r => {
            reject(r);
          }
        )
      }
    })
  }

  static race(promises: MyPromise[]) {
    return new MyPromise((resolve, reject) => {
      for (let i = 0; i < promises.length; i++) {
        promises[i].then(
          v => resolve(v),
          r => reject(r)
        )
      }
    })
  }
}
```