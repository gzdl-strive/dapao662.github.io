---
title: 策略模式
toc: true
date: 2021-08-31 17:17:24
---

## 策略模式定义
>定义一系列的算法 把它们封装起来 并且它们之间可以相互替换
> 核心：将算法的使用和算法的实现分离开来

例子：实现表单的验证

html写法如下
```html
  <form action="http://www.baidu.com" method="post" id="strategyForm">
    用户名:<input
      type="text"
      placeholder="请输入用户名"
      name="username"
    /><br />
    密码:<input type="password" placeholder="密码" name="password" /><br />
    手机号:<input
      type="text"
      placeholder="请输入手机号"
      name="phoneNumber"
    /><br />
    <button>subimt</button>
  </form>
```

**之前的做法**
```js
  //1、获取form表单的dom节点
  let strategyForm = document.getElementById("strategyForm");
  //2、表单提交时进行验证
  strategyForm.onsubmit = function () {
    let { username, password, phoneNumber } = strategyForm;
    //判断用户名是否为空
    if (username.value === "") {
      alert("用户名不能为空");
      return false;
    }
    //判断密码长度
    if (password.value.length < 6) {
      alert("密码长度不能小于6");
      return false;
    }
    //判断手机格式
    if (!/^1[3|5|7|8][0-9]{9}$/.test(phoneNumber.value)) {
      alert("手机号格式错误");
      return false;
    }
  };
```

## 采用策略模式
**定义一系列的算法 把它们封装起来**

- 1、定义策略对象
策略对象-一系列的对象-一系列的业务逻辑
```js
  let strategies = {
    isNonEmpty: function (value, errorMsg) {
      if (value === "") {
        return errorMsg;
      }
    },
    minLength: function (value, length, errorMsg) {
      if (value.length < length) {
        return errorMsg;
      }
    },
    isMobile: function (value, errorMsg) {
      if (!/^1[3|5|7|8][0-9]{9}$/.test(value)) {
        return errorMsg;
      }
    },
  };
```

- 2、假设我们有一个验证类，可以完成验证功能
```js
  //假设， 我们有一个验证类，validator (new Validator)
  //validator.add(dom, rule, msg)
  let validatorFunc = function () {
    let validator = new Validator();
    //添加验证规则
    validator.add(strategyForm.username, "isNonEmpty", "用户名不能为空");
    validator.add(strategyForm.password, "minLength:6", "密码长度不能小于6");
    validator.add(strategyForm.phoneNumber, "isMobile", "手机号格式错误");

    //开启验证
    let errorMsg = validator.start();
    return errorMsg;
  };

  strategyForm.onsubmit = function() {
    let errorMsg = validatorFunc();
    if (errorMsg) {
      alert(errorMsg);
      return false;
    }
  }
```

- 3、封装策略类(完成我们假设的功能)
```js
  //封装策略类：构造函数
  function Validator() {
    //保存验证规则的数组
    this.cache = [];
  }
  Validator.prototype.add = function (dom, rule, errorMsg) {
    let ary = rule.split(':');
    this.cache.push(function() {
      let strategy = ary.shift();
      ary.unshift(dom.value);
      ary.push(errorMsg);
      return strategies[strategy].apply(dom, ary);
    })
  }
  Validator.prototype.start = function () {
    for (let i = 0; i < this.cache.length; i++) {
      let msg = this.cache[i]();
      if (msg) {
        return msg;
      }
    }
  }
```