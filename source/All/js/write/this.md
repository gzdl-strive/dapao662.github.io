---
title: this练习
date: 2021-07-05 21:14:24
toc: true
---

## 1、this在函数中直接使用
```js
function get(content) {
  console.log(content);
}
get('hello');//hello
//相当于
get.call(window, 'hello');
```

## 2、函数作为对象的方法被调用(谁调用我，我就指向谁)
```js
var person = {
  name: '张三',
  run: function(time) {
    console.log(`${this.name} 在跑步 最多${time}min就不行了`);
  }
}
person.run(30);//张三在跑步最多30min就不行了
//相当于
person.run.call(person, 30);
```

所以，如下练习的答案就很轻易得出
```js
var name = 222; //window.name === 222;
var a = {
  name: 111,
  say: function() {
    console.log(this.name);
  }
}
var fun = a.say;
fun(); //相当于fun.call(window); => 222
a.say();//相当于a.say.call(a); => 111

var b = {
  name: 333,
  say: function(fun) {
    fun()
  }
}
b.say(a.say);//=》fun() 相当于fun.call(window); => 222
b.say = a.say;//b.say = function () {console.log(this.name)}
b.say(); //相当于b.say.call(b); //333
```