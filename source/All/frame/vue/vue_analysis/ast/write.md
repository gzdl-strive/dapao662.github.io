---
title: AST
date: 2022-01-21 15:05:24
toc: true
---

## 不加 attrs 的版本

```js
//index.js
import parse from "./parse.js";
let templateString = `<div>
  <h3>你好</h3>
  <ul>
    <li>A</li>
    <li>B</li>
    <li>C</li>
  </ul>
</div>`;

const ast = parse(templateString);
console.log(ast);
```

### parse.js

```js
const parse = (templateString) => {
  //设置一个指针用于遍历
  let index = 0;
  //设置变量用于表示剩余部分
  let rest = "";
  //开始标签匹配正则
  let startTagRegExp = /^\<([a-z]+[1-6]?)\>/;
  //结束标签匹配正则
  let endTagRegExp = /^\<\/([a-z]+[1-6]?)\>/;
  //匹配结束标签前的文字
  let wordRegExp = /^([^\<]+)\<\/[a-z]+[1-6]?\>/;
  //是否全是空
  let emptyStrRegExp = /^\s+$/;
  //准备两个栈,一个用于存储标签，一个用于存储该标签对应的数据数组
  let stack1 = [],
    stack2 = [
      {
        children: [],
      },
    ];

  //遍历: 最后一个的下标是templateString - 1
  while (index < templateString - 1) {
    //修改剩余部分
    rest = templateString.substring(index);
    //我们需要匹配剩余部分的开头是属于什么类型
    if (startTagRegExp.test(rest)) {
      //匹配开始标签
      let tag = rest.match(startTagRegExp)[1];
      stack1.push(tag);
      stack2.push([
        {
          tag: tag,
          children: [],
        },
      ]);
      //移动指针，标签的长度加上<>的长度2
      index += tag.length + 2;
    } else if (endTagRegExp.test(rest)) {
      //匹配结束标签
      let tag = rest.match(endTagRegExp)[1];
      //判断栈1栈顶的标签名是否和当前tag相等,如不相等，说明标签没有正确闭合
      let pop_tag = stack1.pop();
      if (tag === pop_tag) {
        //如果正确封闭了，需要将栈2栈顶的元素弹出，并加入到栈2栈顶的children中
        let pop_arr = stack2.pop();
        //如果栈2还有元素
        if (stack2.length > 0) {
          stack2[stack2.length - 1].children.push(pop_arr);
        }
      } else {
        throw new Error(pop_tag + "没有正确闭合");
      }
      //移动指针，标签的长度加上</>的长度3
      index += tag.length + 3;
    } else if (wordRegExp.test(rest)) {
      //匹配文字，不能全为空
      let word = rest.match(wordRegExp)[1];
      //看word是否全为空
      if (!emptyStrRegExp.test(word)) {
        //不全为空
        //将该文本推入栈中
        stack2[stack2.length - 1].children.push({
          text: word,
          type: 3,
        });
      }
      //指针后移
      index += word.length;
    } else {
      index++;
    }
  }

  return stack2[0].children;
};

export default parse;
```

## 加 attrs 的版本

```js
//index.js
import parse from "./parse.js";
let templateString = `<div>
  <h3 class="myclass a b" data-n="myData" id="myBox">你好</h3>
  <ul>
    <li>A</li>
    <li>B</li>
    <li>C</li>
  </ul>
</div>`;

const ast = parse(templateString);
console.log(ast);
```

修改 parse.js

```js
//parse.js
import parseAttrsString from "./parseAttrsString";

//...
let startTagRegExp = /^\<([a-z]+[1-6]?)(\s[^\<]+)?\>/;
//...

while () {
  //...
  //匹配开始标签
  let attrsString = rest.match(startTagRegExp)[2];
  stack2.push({
    'tag': tag,
    'children': [],
    'attrs': parseAttrsString(attrsString)
  });
  const attrsStringLength = attrsString ? attrsString.length : 0;
  //移动指针，标签的长度 加上<>的长度2, 再加上attrs的长度
  index += tag.length + 2 + attrsStringLength;
  //...
}
```

### parseAttrsString.js
```js
//将attrsString变成数组返回
export default function parseAttrsString(attrsString) {
  if (!attrsString) return [];
  let resultStr = [];
  let isYinHao = false, point = 0;
  for (let i = 0; i < attrsString.length; i++) {
    const str = attrsString[i];
    if (str === '"') {
      isYinHao = !isYinHao;
    } else if (str === ' ' && !isYinHao) {
      //遇见了空格，且不在引号中
      if (!/^\s*$/.test(attrsString.substring(point, i)))
      resultStr.push(attrsString.substring(point, i).trim());
      point = i;
    }
  }
  // 循环结束之后，最后还剩一个属性k="v"
  resultStr.push(attrsString.substring(point).trim())
  //下面的代码将["k=v","k=v","k=v"]变为[{name:k, value:v}, {name:k, value:v}, {name:k,value:v}];
  return resultStr.map(str => {
    const o = str.match(/^(.+)="(.+)"$/);
    return {
      name: o[1],
      value: o[2]
    }
  })
}
```
