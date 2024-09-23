---
title: 递归侦测对象的全部属性
date: 2021-01-17 10:16:24
toc: true
---

![reactive](/assets/vueImg/vueSource/reactive5.png "递归侦测对象的全部属性")

## utils.js
```js
export const def = function (data, key, val, enumerable) {
  Object.defineProperty(data, key, {
    value: val,
    enumerable,
    writable: true,
    configurable: true,
  })
}
```

## Observer类
```js
import defineReactive from './defineReactive';
import { def } from './utils';

export default class Observer {
  constructor(value) {
    // 给实例（this，一定要注意，构造函数中的this不是表示类本身，而是表示实例）添加了__ob__属性，值是这次new的实例
    def(value, '__ob__', this, false);
    // 不要忘记初心，Observer类的目的是：将一个正常的object转换为每个层级的属性都是响应式（可以被侦测的）的object

    this.walk(value);
  }

  //遍历
  walk(value) {
    for(let key in value) {
      defineReactive(value, key);
    }
  }
};
```

## defineReactive修改
```js
import observe from "./observe";

export default function defineReactive(data, key, val) {
  if (arguments.length === 2) {
    val = data[key];
  }
  // 子元素要进行observe，至此形成了递归。这个递归不是函数自己调用自己，而是多个函数、类循环调用
  let childOb = observe(val);

  Object.defineProperty(data, key, {
    enumerable: true,
    configurable: true,
    //getter
    get() {
      console.log('正在访问' + key + '属性');
      return val;
    },
    //setter
    set(newVal) {
      console.log('正在修改' + key + '属性', newVal);
      if (newVal === val) return;
      val = newVal;
      childOb = observe(newVal);//修改的是一个对象时，需要把对象里的属性都变成响应式的
    }
  })
}
```

## observe
```js
import Observer from './Observer';

export default function observe(value) {
  //如果value不是对象，什么都不做
  if (typeof value !== 'object') return;
  //定义ob
  let ob;
  if (value.__ob__ !== undefined) {
    ob = value.__ob__;
  } else {
    ob = new Observer(value);
  }
  return ob;
};
```

```js
//index.js
const obj = {
  a: {
    m: {
      n: 10
    }
  },
  b: 55,
  c: [1, 2, 3]
}

observe(obj);
```