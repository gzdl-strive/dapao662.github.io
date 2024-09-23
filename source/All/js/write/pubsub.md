---
title: 发布订阅模式
toc: true
date: 2021-09-02 16:49:02
---

## 含义说明
发布-订阅模式又叫观察者模式，它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知 **先订阅在发布**

## 作用
1. 支持简单的广播通信，当对象状态发生改变时，会自动通知已经订阅过的对象
2. 可以应用在异步编程中 替代回调函数 可以订阅ajax之后的事件 只需要订阅自己需要的部分(那么ajax调用发布之后就可以拿到消息了)(不需要关系对象在异步运行时候的状态)
3. 对象直接的松耦合 两个对象直接都互相不了解彼此 但是不影响通信 当有新的订阅者出现的时候 发布的代码无需改变 同样订阅的代码也无需改变 只有之前约定的事件的名称没有改变 也不影响订阅
4. vue react之间实习跨组件之间的传值

## 生活中的实例
比如小红最近在淘宝上看上了一双鞋子，但是，联系到卖家后，才发现这双鞋已经卖光了，但是小红对这双鞋又非常喜欢，所以联系商家，问什么时候有货，商家告诉她要等一个星期后才有货，卖家告诉小红，要是你喜欢的话，你可以收藏我们的店铺，等有货的时候通知你，所以小红收藏了此店铺，但与此同时，小光、小花等也喜欢这双鞋，也收藏了该店铺；等来货的时候就依次会通知他们;

## 如何实现一个发布--订阅模式
1. 首先要想好谁是发布者(比如上面的卖家)
2. 然后给发布者添加一个缓存列表，用于存放回调函数来通知订阅者(比如上面的买家收藏了卖家的店铺，卖家通过收藏了该店铺的一个列表名单)
3. 最后就是发布信息，发布者经历这个缓存列表，依次触发里面存放的订阅者回调函数

## 改进异步操作中的强耦合
### 业务场景
假如正在开发一个商城网站，网站里面又header头部、nav导航、消息列表、购物车等模块。这几个模块的渲染又一个共同的前提条件，就是必须先用ajax异步请求获取用户的登录信息。这是很正常的，比如用户的头像和名字要显示在header模块里，而这两个字段都来自用户登录后返回的信息
```js
login.succ(function(data) {
  header.setAvatar(data.avatar);//设置header 模块的头像
  nav.setAvatar(data.avatar);//设置导航模块的头像
  message.refresh(); //刷新消息列表
  cart.refresh();//刷新购物车列表
});
```

### 强耦合
现在必须了解header模块里设置头像的方法叫setAvatar、购物车模块里刷新的方法叫refresh，这种耦合性会使程序变得僵硬，header模块不能随意再改变setAvatar的方法名，它自身的名字也不能被改为header1，header2

- 等到有一天，项目中又新增一个一个收获地址管理的模块，在最后部分假设这行代码：
```js
login.succ(function (data) {
  //...
  address.refresh()//刷新收货地址模块
})
```

### 发布订阅模式 实现低耦合
用发布-订阅模式重写之后，对用户信息感兴趣的业务模块将自行订阅登录成功的消息事件。当登录成功时，登录模块只需要发布登录成功的消息，而业务方接受到消息后，就会进行各自的业务处理，登录模块并不关心业务方究竟要做什么，也不需要去了解它们的内部实现
```js
//登录成功
$.ajax('http://xxx.com?login', function (data) {
  login.trigger('loginSucc', data);//发布登录成功的消息
})
```
- 各模块订阅登录成功的消息
```js
//header模块
let header = (function () {
  login.listen('loginSucc', function (data) {
    header.setAvatar(data.avatar);
  });
  return {
    setAvatar: function (data) {
      console.log('设置header模块的头像');
    }
  }
})();
//nav模块
let nav = (function () {
  login.listen('loginSucc', function (data) {
    nav.setAvatar(data.avatar);
  });
  return {
    setAvatar: function (data) {
      console.log('设置nav模块的头像');
    }
  }
})();
```


## 例子
**商城里面购买鞋子**

### 第一个版本
1. 首先要想好谁是发布者(比如上面的卖家)
2. 然后给发布者添加一个缓存列表，用于存放回调函数来通知订阅者(比如上面的买家收藏了卖家的店铺，卖家通过收藏了该店铺的一个列表名单)
3. 最后就是发布信息，发布者经历这个缓存列表，依次触发里面存放的订阅者回调函数
```js
let shopObj = {}; //定义发布者
shopObj.list = []; //定义缓存列表

//增加订阅者
shopObj.listen = function (fn) {
  this.list.push(fn);
}

//发布消息:遍历缓存列表，依次触发
shopObj.trigger = function () {
  for (let i = 0, fn; fn = this.list[i++];) {
    fn.apply(this, arguments);
  }
}

shopObj.listen(function (color, size) {
  console.log(`颜色是${color}, 尺码是${size}`);
});
shopObj.listen(function (color, size) {
  console.log(`再次颜色是${color}, 再次尺码是${size}`);
});
shopObj.trigger('red', 44);
shopObj.trigger('black', 46);
``` 
**缺点:** 会把订阅的消息发送给所有人，所以我们需要一个特定的标识

### 第二个版本
我们需要为每个订阅者添加特殊标识
```js
let shopObj = {};//定义发布者
shopObj.list = [];//缓存列表 存放的是订阅的函数
//增加订阅者
//key代表特殊标识
shopObj.listen = function (key, fn) {
  //先判断缓存列表中是否存在该标识
  if (!this.list[key]) {
    this.list[key] = [];
  }
  this.list[key].push(fn);
}

//发布消息
shopObj.trigger = function () {
  //先取出标识（这里我们把标识放在第一个实参中，所以我们需要对arguments类数组对象进行操作,arguments对象不具备数组的方法，所以我们需要使用call方法来借用数组方法）
  let key = Array.prototype.shift.call(arguments);
  let fns = this.list[key];
  if (!fns || fns.length === 0) return;
  for (let i = 0, fn; fn = fns[i++];) {
    fn.apply(this, arguments);
  }
}

shopObj.listen('red', function (size) {
  console.log(`尺寸是${size}`);
})
shopObj.listen('black', function (size) {
  console.log(`再次尺寸是${size}`);
})

shopObj.trigger('red', 666);
shopObj.trigger('black', 777);
```

### 第三个版本
把上一个版本进行简单的封装
```js
let event = {
  list: [],
  listen: function (key, fn) {
    if (!this.list[key]) {
      this.list[key] = [];
    }
    this.list[key].push(fn);
  },
  trigger: function () {
    let key = Array.prototype.shift.call(arguments);
    let fns = this.list[key];
    if (!fns || fns.length === 0) return;
    for (let i = 0, fn; fn = fns[i++];) {
      fn.apply(this, arguments);
    }
  }
}
```
**我们还需要新增一个功能就是取消订阅**
```js
event.remove = function (key, fn) {
  let fns = this.list[key];
  if (!fns) return false;
  if (!fn) {
    fn && (fns.length === 0);
  } else {
    for (let i = fns.length - 1; i >= 0; i--) {
      let _fn = fns[i];
      if (_fn === fn) {
        fns.splice(i, 1);
      }
    }
  }
}
```

### 第四版
进一步封装
```js
let Event = (function() {
  let list = {}, listen, trigger, remove;
  listen = function (key, fn) {
    if (!list[key]) {
      list[key] = [];
    }
    list[key].push(fn);
  };
  trigger = function () {
    let key = Array.prototype.shift.call(arguments);
    let fns = list[key];
    if (!fns || fns.length === 0) return;
    for (let i = 0, fn; fn = fns[i++];) {
      fn.apply(this, arguments);
    }
  };
  remove = function (key, fn) {
    let fns = list[key];
    if (!fns) return false;
    if (!fn) {
      fn && (fns.length === 0);
    } else {
      for (let i = fns.length - 1; i >= 0; i--) {
        let _fn = fns[i];
        if (_fn === fn) {
          fns.splice(i, 1);
        }
      }
    }
  };
  return {
    listen,
    trigger,
    remove
  }
})();

Event.listen('red', function (size) {
  console.log(`红色：尺码是${size}`);
})
Event.listen('black', function (size) {
  console.log(`黑色：尺码是${size}`);
})

Event.trigger('red', 42);
Event.trigger('black', 38);
```

