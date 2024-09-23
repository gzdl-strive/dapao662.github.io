---
title: 数据响应式
date: 2021-09-13 21:13:24
toc: true
---

>目标：实现简易数据响应式

```js
function defineReactive(obj, key, val) {
  //使用Object.defineProperty
  //主要是,get和set
  Object.defineProperty(obj, key, {
    get() {
      console.log('get', key);
      return val;//生成闭包
    },
    set(newVal) {
      //只有当新值和旧值不想等时
      if (newVal !== val) {
        console.log('set', key);
        val = newVal;
      }
    }
  })
}
```

**当前数据只能通过defineReactive一个个定义，所以我们需要顶一个observe方法，来一次性定义响应式数据**

```js
//observe->传入一个对象，把该对象下的数据都变成响应式数据
function observe(obj) {
  if (typeof obj !== object || obj === null) {
    return;
  }

  //遍历所有Key,做响应式处理
  Object.keys(obj).forEach(key => {
    defineReactive(obj, key, obj[key]);
  })
}
```

**当前代码存在问题：如果key对应的值是一个对象，那么该对象却不是响应式的，所以我们应该递归其值，把它变成响应式数据**
我们知道observe会遍历给定对象的key,把Key变成响应式数据，所以，我们可以在定义响应式之前先判断当前key所对应的值是否需要变成响应式
```js
function defineReactive(obj, key, val) {
  //直接调用observe
  //递归，处理嵌套（val如果是个对象，就需要递归处理）
  observe(val);

  //...
}
```

**这时还存在一种情况，当我们的新值是一个对象时，应该重新赋值了，所以该新对象里的数据不再是响应式数据了，所以我们在set中应该重新把它变成响应式数据**
```js
function defineReactive(obj, key, val) {
  //...

  Object.defineProperty(obj, key, {
    get() {
      //...
    },
    set(newVal) {
      if (newVal !== val) {
        console.log('set', key);
        //保证：如果新值是一个对象，再次做响应式处理
        observe(newVal);
        val = newVal;
      }
    }
  })
}
```

**接下来，我们需要处理动态新增key**
```js
//使用set函数
function set(obj, key, val) {
  //相当于只是做了一个转发
  defineReactive(obj, key, val);
}
```

**其实我们的功能已经差不多了，但是我们目前只是在处理对象的响应式，我们可能还存在数组响应式数据**
要想实现数组响应式
>1. 找到数组原型
>2. 覆盖那些能够修改数组的更新方法，使其可以通知更新
>3. 将得到的新的原型设置到数组实例原型上

```js
//1.替换数组原型中7个方法
const originProto = Array.prototype;
//备份一份，修改备份
const arrayProto = Object.create(originProto);
['push', 'pop','shift', 'unshift', 'slice', 'sort', 'reverse'].forEach(method => {
  arrayProto[method] = function () {
    //原始操作
    originProto[method].apply(this, arguments);
    //覆盖操作:通知更新
    console.log('数组执行' + method + '操作');
  }
})

//数组响应式添加
function observe(obj) {
  if (typeof obj !== 'object' || obj === null) {
    return;
  }

  //判断传入的类型
  if (Array.isArray(obj)) {
    //覆盖原型，替换7个变更操作
    obj.__proto__ = arrayProto;
    //传入的数组中可能还是其他的数据类型，对数组内部的元素进行响应化
    const keys = Object.keys(obj);
    for (let i = 0; i < keys.length; i++) {
      //递归响应式
      observe(obj[i]);
    }
  } else {
    //之前的对象响应式
    Object.keys(obj).forEach(key => {
      defineReactive(obj, key, obj[key]);
    })
  }
}
```
