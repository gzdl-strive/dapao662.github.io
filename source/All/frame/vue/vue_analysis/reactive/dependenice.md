---
title: 依赖收集Dep类和Watcher类
date: 2022-01-17 10:44:24
toc: true
---

**Dep类和Watcher类**
![reactive](/assets/vueImg/vueSource/reactive7.png "reactive")

依赖就是`Watcher`。只有Watcher触发的getter才会收集依赖，哪个Watcher触发了getter，就把哪个Watcher收集到Dep中。 
Dep使用发布订阅模式，当数据发生变化时，会循环依赖列表，把所有的Watcher都通知一遍。 
代码实现的巧妙之处：Watcher把自己设置到全局的一个指定位置，然后读取数据，因为读取了数据，所以会触发这个数据的getter。在getter中就能得到当前正在读取数据的Watcher，并把这个Watcher 收集到Dep中

![reactive](/assets/vueImg/vueSource/reactive8.png "reactive")

## 修改defineReactive方法
>在读取属性时，触发依赖收集,修改属性时，通知更新
```js
import Dep from "./Dep";

const dep = new Dep();
//...
get() {
  //...
  //使用一个全局变量来判断是否处于依赖收集节点
  if (Dep.target) {
    //依赖收集
    dep.depend();
  }
  return val;
},
set() {
  //...
  //发布订阅模式，通知dep,循环通知Watcher实例进行更新
  dep.notify(); 
}
```

## Dep类
```js
export default class Dep {
  constructor() {
    console.log('Dep被执行了');
    // 用数组存储自己的订阅者。subs是英语subscribes订阅者的意思。
    // 这个数组里面放的是Watcher的实例
    this.subs = [];
  }

  //通知更新
  notify() {
    console.log('notify');
    //浅克隆一份
    const subs = this.subs.slice();
    //遍历
    for (let i = 0; i < subs.length; i++) {
      //每一项sub都是一个watcher
      subs[i].update();
    }
  }

  //添加订阅
  addSub(sub) {
    this.subs.push(sub);
  }

  //添加依赖
  depend() {
    // Dep.target就是一个我们自己指定的全局的位置，你用window.target也行，只要是全局唯一，没有歧义就行
    if (Dep.target) {
      this.addSub(Dep.target);
    }
  }
}
```

## Watcher类
```js
import Dep from "./Dep";

export default class Watcher {
  constructor(target, expression, callback) {
    console.log('Watcher类构造器执行');
    this.target = target;
    this.getter = parsePath(expression);
    this.callback = callback;
    this.value = this.get();
  }

  get() {
    //进入依赖收集阶段。让全局的Dep.target设置为Watcher本身，那么就是进入依赖收集阶段
    Dep.target = this;//实例对象watcher
    const obj = this.target;
    let value;

    //只要能找，就一直找
    try {
      value = this.getter(obj);
    } finally {
      Dep.target = null;
    }

    return value;
  }

  getAndInvoke(cb) {
    const value = this.get();

    if (value !== this.value || typeof value === 'object') {
      const oldValue = this.value;
      this.value = value;
      cb.call(this.target, value, oldValue);
    }
  }

  run() {
    this.getAndInvoke(this.callback);
  }

  update() {
    this.run();
  }
}

function parsePath(str) {
  let segments = str.split('.');

  return (obj) => {
    for (let i = 0; i < segments.length; i++) {
      if (!obj) return;
      obj = obj[segments[i]];
    }
    return obj;
  }
}
```
