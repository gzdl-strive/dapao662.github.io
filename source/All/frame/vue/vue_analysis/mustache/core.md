---
title: Mustache核心机理
date: 2022-01-07 11:28:24
toc: true
---

## 使用正则实现简单模板数据填充

```js
var templateStr = "<h1>我买了一个{{thing}}，花了{{money}}元，好{{mood}}</h1>";

var data = {
  thing: "白菜",
  money: 5,
  mood: "激动",
};

// 最简单的模板引擎的实现机理，利用的是正则表达式中的replace()方法。
// replace()的第二个参数可以是一个函数，这个函数提供捕获的东西的参数，就是$1
// 结合data对象，即可进行智能的替换
function render(templateStr, data) {
  return templateStr.replace(/\{\{(\w+)\}\}/g, function (findStr, $1) {
    return data[$1];
  })
}

let result = render(templateStr, data);
```

**在较为简单的实例情况下，可以使用正则实现，但是当情况复杂时，正则表达式的思路肯定就不行了。**

## mustache机理
![1](/assets/vueImg/vueSource/mustache2.png "核心机理")

### 什么是tokens
- tokens是一个**JS的嵌套数组**,说白了，就是**模板字符串的JS表示**
- 它是"抽象语法树"、"虚拟节点"等等的开山鼻祖
![3](/assets/vueImg/vueSource/mustache3.png "tokens")

![4](/assets/vueImg/vueSource/mustache4.png "tokens")

>mustache库底层重点要做两件事情：
>1. 将模板字符串编译成tokens形式
>2. 将tokens结合数据，解析为dom字符串

### 将模板字符串变成tokens
![5](/assets/vueImg/vueSource/mustache5.png "tokens")

**通过栈将零散的tokens嵌套起来**
![6](/assets/vueImg/vueSource/mustache6.png "tokens")

