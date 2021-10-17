---
title: MVVM模型
date: 2021-10-08 09:55:24
toc: true
---

### MVVM模型
  1. M：模型(Model) => data中的数据
  2. V: 视图(View) => 模板代码
  3. VM: 视图模型(ViewModel): Vue实例
### 观察发现：
  1、data中所有的属性，最后都出现在vm上(vue底层进行了数据代理)
  2、vm身上所有的属性以及Vue原型上所有属性，在Vue模板中都可以直接使用

![mvvm](/assets/vueImg/mvvm.png "MVVM")

## 数据代理

### 回顾Object.defineProperty()方法
```js
let number = 18;
let person = {
  name: '张三',
  sex: '男'
};
Object.defineProperty(person, 'age', {
  // value: 18,
  // enumerable: true,//控制属性是否可以枚举，默认为false
  // writable: true,//控制属性是否可以被修改，默认为false
  // configurable: true,//控制属性是否可以被删除，默认值是false

  //当有人读取person的age属性时，get函数(getter)就会被调用，且返回值就是age的值
  get() {
    console.log('有人读取了age属性');
    return number;
  },
  //当有人修改person的age属性时，set函数(setter)就会被调用，且会收到修改的具体值
  set(newVal) {
    console.log('有人修改了age属性,且值是: ', newVal);
    number = newVal;
  }
})
```


### 何为数据代理
>数据代理：通过一个对象代理另一个对象中属性的操作(读/写)
```js
let obj = { x: 100 };
let obj2 = { y: 200 };

Object.defineProperty(obj2, 'x', {
  get() {
    return obj.x;
  },
  set(newVal) {
    obj.x = newVal;
  }
})
```

### Vue中的数据代理
1. Vue中的数据代理：
  - 通过vm对象来代理data对象中属性的操作(读/写)
2. Vue中数据代理的好处：
  - 更加方便的操作data中的数据
3. 基本原理：
  - 通过Object.defineProperty()把data对象中的所有属性添加到vm上
  - 为每一个添加到vm上的属性，都指定一个getter/setter
  - 在getter/setter内部去操作(读/写)data中对应的属性
```html
  <div id="app">
    <h2>名称：{{ name }}</h2>
    <h2>武器：{{ weapon }}</h2>
  </div>
  <script>
    const vm = new Vue({
      data() {
        return {
          name: 's1mple',
          weapon: 'AWP, M4A1, Ak47-->666',
        }
      }
    })
    vm.$mount('#app');
  </script>
```