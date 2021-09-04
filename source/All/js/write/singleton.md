---
title: 单例模式
toc: true
date: 2021-08-31 15:56:24
---

## 单例模式的理解
>定义：1、只有一个实例 2、可以全局的访问

主要解决：一个全局使用的类，频繁的创建和销毁

何时使用：当你想控制实例的数目 节省系统化资源的时候

如何实现：**判断系统是否已经有这个单例，如果有则返回，没有则创建**

单例模式优点：内存中只要一个实例 减少了内存的开销 尤其是评分的创建和销毁实例(比如说是首页页面的缓存)

使用场景：1、全局的缓存 2、弹窗

## es5 实现单例模式
- 需求 实现一个登录的弹窗

1、 加载完成的时候 已经创建好这个弹窗了 一开始是隐藏的状态 弹窗出现
```html
  <button id="btn">click</button>
  <script>
    let btn = document.getElementById('btn');
    let loginLayer = (function() {
      let div = document.createElement('div');
      div.innerHTML="我是登录的弹窗";
      div.style.display = 'none';
      document.body.appendChild(div);
      return div;
    })();
    btn.onclick = function () {
      loginLayer.style.display = 'block';
    }
  </script>
```
**缺点:** 资源的浪费(如果用户是以游客的身份进入，根本不需要创建dom,所以造成资源的浪费...)

2、 点击的时候创建弹窗
解决：不会造成资源的浪费
```js
  let createLoginLayer = function () {
    let div = document.createElement("div");
    div.innerHTML = "我是登录的弹窗";
    div.style.display = "none";
    document.body.appendChild(div);
    return div;
  };

  btn.onclick = function () {
    let loginLayer = createLoginLayer();
    loginLayer.style.display = 'block';
  }
```
**缺点：** 频繁的创建和销毁

3、单例
- 判断系统是否已经有这个单例 如果有在返回 没有则创建
- 有一个标记在内存中 做一个标记 ---> 需要存在内存中 ---> 闭包

```js
  let createLoginLayer = (function () {
    let div;//使用闭包存储在内存中
    return function () {
      if (!div) {
        div = document.createElement("div");
        div.innerHTML = "我是登录的弹窗";
        div.style.display = "none";
        document.body.appendChild(div);
      }
      return div;
    }
  })();

  btn.onclick = function () {
    let loginLayer = createLoginLayer();
    loginLayer.style.display = 'block';
  }
```

还可以改进吗?
上面的代码我们如果还想创建一个全局的iframe，我们就需要把代码重新拷贝一份

>**单例模式是一种思想，单一职责的思想**

4、弹窗可以拆分为一下两点：
1. 形成单例
2. 创建弹窗

形成单例->从代码里面抽取处理(核心代码：闭包)
```js
  //形成单例，创建弹窗
  //1、形成单例->单例的职责
  let getSingle = function (fn) {
    let result;
    return function() {
      return result || (result = fn.apply(this, arguments))
    }
  }
  //2、创建弹窗的职责
  let createLoginLayer = function () {
    let div = document.createElement('div');
    div.innerHTML = '我是登录的弹窗-单例优化';
    div.style.display = 'none';
    document.body.appendChild(div);
    return div;
  }

  let createSingleLogin = getSingle(createLoginLayer);
  btn.onclick = function () {
    let loginLayer = createSingleLogin();
    loginLayer.style.display = 'block';
  }
```

## es6实现单例模式
- class类可以看成是es5构造函数的语法糖

