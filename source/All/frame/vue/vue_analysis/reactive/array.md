---
title: 数组的响应式处理
date: 2022-01-17 10:31:24
toc: true
---

现在我们只是大概的对对象进行劫持，没有细分

![reactive6](/assets/vueImg/vueSource/reactive6.png "reactive")

## array.js
```js
import { def } from './utils';

//得到Array.prototype
const arrayPrototype = Array.prototype;

//以Array.prototype为原型创建arrayMethods对象，并暴露出去
export const arrayMethods = Object.create(arrayPrototype);

//要被重写的7个数组方法
const methodsNeedChange = ['push', 'pop', 'shift', 'unshift', 'sort', 'reverse', 'splice'];

methodsNeedChange.forEach(methodName => {
  //备份原来的方法，因为push、pop等7个函数的功能不能被剥夺
  const original = arrayPrototype[methodName];

  //定义新的方法
  def(arrayMethods, methodName, function () {
    //先恢复原来的功能
    const result = original.apply(this, arguments);
    //把类数组转换成数组
    const args = [...arguments];
    // 把这个数组身上的__ob__取出来，__ob__已经被添加了，为什么已经被添加了？因为数组肯定不是最高层，比如obj.g属性是数组，obj不能是数组，第一次遍历obj这个对象的第一层的时候，已经给g属性（就是这个数组）添加了__ob__属性。
    const ob = this.__ob__;

    //有三种方法push\unshift\splice能够插入新项，现在要把插入的新项也变成observe的
    let inserted = [];

    switch(methodName) {
      case 'push':
      case 'unshift': 
        inserted = args;
        break;
      case 'splice':
        // splice格式是splice(下标, 数量, 插入的新项)
        inserted = args.slice(2);
        break;
      default:
        inserted = [];
        break;
    }

    // 判断有没有要插入的新项，让新项也变为响应的
    if (inserted) {
      ob.observeArray(inserted);
    }
    
    console.log('重写重写重写数组方法');
    
    return result;
  }, false);
});
```

## 修改Observer类
```js
//...
import { arrayMethods } from './array'

export default class Observer {
  constructor() {
    //...

    //响应式处理数组
    if (Array.isArray(value)) {
      // 如果是数组，要非常强行的蛮干：将这个数组的原型，指向arrayMethods
      Object.setPrototypeOf(value, arrayMethods);
      //让这个数组进行observe
      this.observeArray(value);
    } else {
      this.walk(value);
    }
  }

  //...

  //数组进行observe
  observeArray(arr) {
    for(let i = 0, l = arr.length; i < l; i++) {
      //逐项进行observe
      observe(arr[i]);
    }
  }
}
```