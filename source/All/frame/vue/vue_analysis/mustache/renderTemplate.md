---
title: tokens结合数据生成dom字符串
date: 2022-01-08 21:27:24
toc: true
---

## 第一版renderTemplate
**现在我们已经通过编译生成了tokens数组**
```js
export default function renderTemplate(tokens, data) {
  //结果字符串
  let resultStr = '';
  //遍历tokens
  for (let i = 0; i < tokens.length; i++) {
    let token = tokens[i];
    if (token[0] === 'text') {
      resultStr += token[1];
    } else if (token[0] === 'name') {
      //如果是name类型，那么就直接使用它的值
      result += data[token[1]];
    } else if (token[0] === '#') {
      //...
    }
  }
  return resultStr;
}
```
先不处理`#`类型的token,我们看`name`类型的数据，如果存在`a.b.c`有逗号的形式，那么`data[token[1]]`就会变成`data[a.b.c]`就会产生错误，所以我们这里通过一个函数来处理这种形式

## lookup函数
>功能是可以在dataObj对象中，寻找用连续点符号的keyName属性

例如：dataObj是
```js
{
  a: {
    b: {
      c: 100
    }
  }
}
```
那么`lookup(dataObj, 'a.b.c')`结果就是`100`

```js
export default function lookup(dataObj, keyName) {
  //判断keyName是否包含“.”,但是不能是.本身，因为Mustache模板引擎里有简写数组形式就是以一个.简写
  if (keyName !== '.' && keyName.indexOf('.') !== -1) {
    //先拆开"."
    let keys = keyName.split('.');//["a", "b", "c"]
    //设置一个临时变量，这个临时变量用于周转，一层一层找下去
    let temp = dataObj;
    //遍历keys
    for (let i = 0; i < keys.length; i++) {
      let key = keys[i];
      temp = temp[key]
    }
    return temp;
  }
  //如果是不包含".",直接返回
  return dataObj[keyName];
}
```

## 第二版renderTemplate
```js
import lookup from './lookup';

export default function renderTemplate(tokens, data) {
  for (let i = 0; i < tokens.length; i++) {
    let token = tokens[i];
    if (token[0] === 'text') {
      resultStr += token[1];
    } else if (token[0] === 'name') {
      result += lookup(data, token[1]);
    }
    //...
  }
  return resultStr;
}
```
现在我们需要处理`#`类型的token了

## parseArray
>处理数组，结合renderTemplate实现递归
>注意：这个函数接收的参数是token,而不是tokens
>token就是一个简单的`['#', 'students', []]`
```js
import lookup from './lookup';
import renderTemplate from './renderTemplate';

export default function parseArray(token, data) {
  //结果字符串
  let resultStr = '';
  //得到整体数据data中这个数组要使用的部分
  //lookup接收两个参数，第一个是data对象，第二个是keyName
  let val = lookup(data, token[1]);
  /*
  val值如下：
    [
      { name: "小明", hobbies: ["编程", "游泳"] },
      { name: "小红", hobbies: ["看书", "弹琴", "画画"] },
      { name: "小强", hobbies: ["锻炼"] },
    ]
  */

  //遍历数组
  //注意，这时需要遍历的不再是token数组了，而是遍历数据,数组中的数据有几条，就要遍历几条。
  //需要增加一个.为键名的项，因为可能存在简单数组写法
  for (let i = 0; i < val.length; i++) {
    resultStr += renderTemplate(token[2], {
      ...val[i],
      ".": val[i]
    })
  }

  return resultStr;
}
```

## 第三版renderTemplate
```js
import lookup from "./lookup";
import parseArray from "./parseArray";

export default function renderTemplate(tokens, data) {
  let resultStr = '';
  for(let i = 0; i < tokens.length; i++) {
    let token = tokens[i];
    if (token[0] === 'text') {
      resultStr += token[1];
    } else if (token[0] === 'name') {
      resultStr += lookup(data, token[1]);
    } else if (token[0] === '#'){
      //新增
      resultStr += parseArray(token, data);
    }
  }
  return resultStr;
}
```
**现在我们已经生成dom字符串，只需要直接挂载到DOM节点上就能展示出来了**