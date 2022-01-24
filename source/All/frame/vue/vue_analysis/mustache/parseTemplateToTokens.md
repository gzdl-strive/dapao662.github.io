---
title: 解析模板为tokens
date: 2022-01-07 15:57:24
toc: true
---

## Scanner 类

> 该类用于扫描模板，并返回特定内容

```js
//Scanner.js
export default class Scanner {
  construtor(templateStr) {
    //将模板字符串写到实例身上
    this.templateStr = templateStr;
    //指针：表示当前运动到的位置
    this.pos = 0;
    //尾巴，一开始就是模板字符串原文
    this.tail = templateStr;
  }

  //指针是否已经到头，返回一个布尔值。end of string
  eos() {
    return this.pos >= this.templateStr.length;
  }

  //让指针进行扫描，直到遇见指定内容结束，并且能够返回之前路过的内容
  scanUtil(stopTag) {
    //用于记录执行本方法的初始位置(因为要返回路过的内容)
    const pos_backup = this.pos;
    //当指针还没遍历完字符串且尾巴的开头不是stopTag，就说明还没有扫描到stopTag
    //substring(start,stop): 包含起始位置，不包含结束位置
    while (!this.eos() && this.tail.indexOf(stopTag) !== 0) {
      //当前指针后移
      this.pos++;
      //改变尾巴
      this.tail = this.templateStr.substring(this.pos);
    }
    return this.templateStr.substring(pos_backup, this.pos);
  }

  //功能弱，就是走过指定内容，没有返回值
  scan(tag) {
    if (this.tail.indexOf(tag) === 0) {
      //tag有多长，比如{{长度为2，就让指针后移多少
      this.pos += tag.length;
      //尾巴也需要改变，改变尾巴从当前指针位置开始，到最后
      this.tail = this.templateStr.substring(this.pos);
    }
  }
}
```

## 第一版parseTemplateToTokens
```js
import Scanner from './Scanner.js';

export default function parseTemplateToTokens(templateStr) {
  //结果数组
  const tokens = [];

  //创建扫描器
  const scanner = new Scanner(templateStr);
  let words;

  //让扫描器工作
  while (!scanner.eos()) {
    //收集开始标记之前出现的文字
    words = scanner.scanUtil('{{');
    if (words !== '') {
      // 尝试写一下去掉空格，只能判断是普通文字的空格，还是标签中的空格
      //标签中的空格不能去掉
      let isInJJH = false;//标志位：判断空格是否在标签体内
      let _words = '';
      for (let i = 0; i < words.length; i++) {
        //判断是否在标签体内
        if (words[i] === '<') {
          isInJJH = true;
        } else if (words[i] === '>') {
          isInJJH = false;
        }
        //如果当前项不是空格，那么之间拼接起来即可
        if (!/\s+/.test(words[i])) {
          _words += words[i];
        } else {
          //是空格，只有在标签体内才拼接起来
          isInJJH && (_words += words[i]);
        }
      }
      tokens.push(['text', _words]);//收集
    }
    //过双大括号
    scanner.scan('{{');
    words = scanner.scanUtil('}}');//双大括号里面的内容
    if (words !== '') {
      if (words[0] === '#') {
        //存起来，从下标为1开始，因为下标为0是#
        tokens.push(['#', words.substring(1)]);
      } else if (words[0] === '/') {
        tokens.push(['/', words.substring(1)]);
      } else {
        tokens.push(['name', words]);
      }
    }
    //过双大括号
    scanner.scan('}}');
  }

  return tokens;
}
```
**输出如下所示**
![tokens](/assets/vueImg/vueSource/mustache7.png "tokens")

可以看出，现在的tokens比较零散，我们还需要另外一个类帮我们整合

## nestTokens 类

> 整合零散的 tokens,函数的功能就是折叠 tokens,将#和/之间的 tokens 整合起来，作为它的下标

```js
export default function nestTokens(tokens) {
  //结果数组
  const nestedTokens = [];
  //栈结构，用于存放小tokens
  let sections = [];
  //收集器,天生指向nestedTokens结果数组，引用类型值，所以指向的是同一数组
  //收集器的指向会变化，当遇见#的时候，收集器会指向这个token的下标为2的新数组
  let collector = nestedTokens;

  for (let i = 0; i < tokens.length; i++) {
    let token = tokens[i];

    switch(token[0]) {
      case '#':
        //收集器先放入token
        collector.push(token);
        //入栈
        sections.push(token);
        //收集器需要改变指向，给token添加下标为2的项，并且让收集器指向它
        collector = token[2] = [];
        break;
      case '/':
        //出栈
        sections.pop();
        //改变收集器的指向，指向栈顶元素下标为2的数组
        collector = sections.length > 0 ? sections[sections.length - 1][2] : nestedTokens;
        break;
      default:
        collector.push(token);
    }
  }
  return nestedTokens;
}
```

## 第二版parseTemplateToTokens
**修改parseTemplateToTokens.js的代码**
```js
//parseTempalteToTokens.js
import nestTokens from './nestTokens';

export default function parseTemplateToTokens(templateStr) {
  //...

  // return tokens;
  return nestTokens(tokens);
}
```
