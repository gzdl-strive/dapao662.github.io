---
title: ECMAScript 9 新特性
date: 2021-11-16 10:19:02
toc: true
---

## Rest/Spread 属性

Rest 参数与 spread 扩展运算符在 ES6 中已经引入，不过 ES6 中只针对于数组，在 ES9 中为对象提供了像数组一样的 rest 参数和扩展运算符

```js
function connect({ host, port, ...user }) {
  console.log(host);
  console.log(port);
  console.log(user);
}
connect({
  host: "127.0.0.1",
  port: 3306,
  username: "root",
  password: "root",
  type: "master",
});
```

## 正则表达式命名捕获组

ES9 允许命名捕获组使用符号『?<name>』,这样获取捕获结果可读性更强

```js
let str = '<a href="http://www.baidu.com">百度</a>';
const reg = /<a href="(?<url>.*)">(?<text>.*)<\/a>/;
const result = reg.exec(str);
console.log(result.groups.url);
console.log(result.groups.text);
```

## 正则表达式反向断言

ES9 支持反向断言，通过对匹配结果前面的内容进行判断，对匹配进行筛选

```js
//声明字符串
let str = "JS5211314 你知道么 555 啦啦啦";
//正向断言
const reg = /\d+(?=啦)/;
const result = reg.exec(str);
//反向断言
const reg = /(?<=么)\d+/;
const result = reg.exec(str);
console.log(result);
```

## 正则表达式 dotAll 模式

正则表达式中点.匹配除回车外的任何单字符，标记『s』改变这种行为，允许行终止符出现

```js
let str = `
<ul>
 <li>
 <a>肖生克的救赎</a>
 <p>上映日期: 1994-09-10</p>
 </li>
 <li>
 <a>阿甘正传</a>
 <p>上映日期: 1994-07-06</p>
 </li>
</ul>`;
//声明正则
const reg = /<li>.*?<a>(.*?)<\/a>.*?<p>(.*?)<\/p>/gs;
//执行匹配
const result = reg.exec(str);
let result;
let data = [];
while ((result = reg.exec(str))) {
  data.push({ title: result[1], time: result[2] });
}
//输出结果
console.log(data);
```
