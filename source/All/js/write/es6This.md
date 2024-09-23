---
title: 箭头函数中的this
date: 2021-07-05 21:35:24
toc: true
---

## 箭头函数中的this
>* 箭头函数中的this是在定义函数的时候绑定，而不是在执行函数的时候绑定
>* 箭头函数中，this指向的固定化，并不是因为箭头函数内部有绑定的this的机制，实际原因是箭头函数根本没有自己的this,导致内部的this就是外层代码块的this.正是因为它没有this,所以也就不能用作构造函数。


>* 箭头函数中的this是在定义函数的时候绑定
```js
var x = 11;
var obj = {
  x: 22,
  say: () => {
    console.log(this.x);
  }
}
obj.say();
```
**所谓的定义时候绑定，就是this是继承自父执行上下文中的this,比如这里的箭头函数中的`this.x`,箭头函数本身域say平级以`key:value`的形式，也就是箭头函数本身所在的对象为`Obj`,而obj的父执行上下文就是`window`,因此这里的`this.x`实际上表示的是`window.x`,因此输出的是`11`.**

```js
var obj = {
  birth: 1990,
  getAge: function() {
    var b = this.birth;//obj.getAge.call(obj); => 1990
    var fn = () => new Date().getFullYear() - this.birth; //箭头函数中没有this,this是父执行上下文的，fn函数与箭头函数平级，那么箭头函数所在的执行上下文就是getAge所对应的函数，那么箭头函数的父执行上下文就是obj
    return fn();
  }
};
obj.getAge();
```
**例子中的箭头函数是在getAge方法中定义的，因此，`getAge方法`的父执行上下文是`obj`,因此箭头函数的this指向的就是`obj对象`**


