---
title: JSON与JS对象
date: 2020-10-27 10:40:23
tags: js
---
## JSON vs JS对象

### JSON定义
>* JSON是JS的一种简单数据格式，JSON是JavaScript原生格式，它是一种严格的js对象的格式
>* JSON的属性名必须有双引号，如果值是字符串，也必须是双引号

**JSON只是一种数据格式**
```js
var obj = {}; //这只是JS对象
var obj1 = {width:100, height:200}; //JS对象
var obj2 = {'width':100, 'height':100}//JS对象
var obj3 = {"width":100, "name": "rose"}; //JSON格式的JS对象
var obj4 = '{"width":100, "height":50, "name":"rose"}'; //JSON格式的字符串
var array = [
    {"width":100, "height":100},
    {"width":100, "name":"rose"},
]//JSON格式的数组
```

### 区别
| 区别 | JSON | JavaScript对象 |
|:------:|:-----:|:------:|
| 含义 | 仅仅是一种数据格式 | 表示类的实例 |
| 传输 | 可以跨平台数据传输，速度快 | 不能传输 |
| 表现 | 1.键值对方式，键必须加双引号 2.值不能是方法函数，不能是undefined/NaN | 1.键值对方式，键不加引号 2.值可以是函数、对象、字符串、数字、boolean 等 |
| 相互转换 | Json转换Js对象1.JSON.parse(JsonStr); (不兼容IE7) 2.eval("("+jsonStr+")"); (兼容所有浏览器,但不安全，会执行json里面的表达式?)| js对象转换JsonJSON.stringify(jsObj); |
