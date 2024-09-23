---
title: 正则表达式
date: 2020-12-22 10:34:02
tag: js
toc: true
---

## 常用字符
* [0-9]匹配单个数字0-9
* [a-z]匹配单个小写字母
* [A-Z]匹配单个大写字母
* \s匹配所有空白字符，如空格换行
* \n匹配所有换行符
* \b匹配边界如字符之间的空格
* \d匹配一个数字
* \w匹配一个字母或数字

## 特殊字符
* ^ 匹配输入字符串的开始位置，用在[]中表示非 1、/^http/匹配以http开头的字符串2、/[^a-zA-Z]/匹配非字母
* $ 匹配输入字符串的结尾位置 /.com$/匹配以.com结尾的字符串
* | 或 /a|b/匹配a或b
* . 小数点匹配换行符\n之外的任何单个字符 /./匹配换行符之外的其他字符
* [] 中括号匹配一个字符 /[csgorushb]/匹配字母csgorushb中的一个
* () 小括号表示一个子表达式分组 匹配的子表达式可以用于以后使用
* {} 大括号表示限定一个表达式多少次 {n}匹配n次;{n,}匹配最少n次;{n,m}匹配n-m次
* `+` 匹配前面的子表达式一次或多次 /[0-9]+/匹配一个数字或多个数字
* `*` 匹配前面的子表达式0或多次 /[0-9]* /匹配0或多次数字
* ? 匹配前面表达式0次或一次,指明一个非贪婪限定符 1、/[0-9]?/ 2、/<.*?>/匹配一个标签如`<p>`

>匹配特殊字符本身时需要转义，共有以下几个：
`* . ? + $ ^ [ ] ( ) { } | \ /`

其中` / `在字面量中需要转义，在构造函数中不需要，如下匹配一个斜杠` / `
```js
const reg = /\//
const reg = new RegExp('/')
```
在字面量中带一个转义符` \ `的用构造函数写要带两个转义符` \\`，如下匹配一个字符串:
```js
const reg = /\./
const reg = new RegExp('\\.')
```

## 常用方法
### 字面量
```js
const reg = /[0-9a-z]/g;
const reg1 = new RegExp('[0-9a-z]','g');
// 其中字面量中不能包含变量，构造函数中可以使用变量：
const name2 = '正则'
const reg2 = new RegExp(`我的名字叫${name2}`)
```
**经常会用` reg.test(str) `方法来判断字符串中是否匹配到了正则表达式**
```js
const reg3 = /[0-9]/g;
const str = `文本中有没有数字123等`;
if (reg3.test(str)) {
    console.log('匹配成功')//匹配成功
}
```
**也经常用`str.replace(reg, '') `方法来替换字符串中的内容：**
```js
if (reg3.test(str)) {
    const newStr = str.replace(reg3, "x");
    console.log(newStr);//文本中有没有数字xxx等
}
```
**也会用到` str.match(reg) `方法来获取匹配到的内容（也可以用`reg.exec(str)`**
```js
const reg4 = /[0-9]+[.][0-9]+[.][0-9]+/g;
const str2 = '正则表达式匹配5555.66.999';
console.log(str2.match(reg4));//[ '5555.66.999' ]
//match 中的正则表达式如果使用g标志，则将返回与完整正则表达式匹配的所有结果，但不会返回捕获组。
```

## 贪婪&非贪婪
> `+` 和 `*` 限定符都是贪婪的，它们会尽可能多的匹配文字。在它们的后面加上一个`?`就可以实现**非贪婪或最小匹配**
### 贪婪-默认都是贪婪的
```js
const str3 = '<h1>正则表达式</h1>'
const reg5 = /<.*>/;
console.log(str3.match(reg5));
/*
[
  '<h1>正则表达式</h1>',
  index: 0,
  input: '<h1>正则表达式</h1>',
  groups: undefined
]
```
### 非贪婪
```js
const str4 = '<h1>正则表达式</h1>';
const reg6 = /<.*?>/
console.log(str4.match(reg6));//[ '<h1>', index: 0, input: '<h1>正则表达式</h1>', groups: undefined ]
```

## 捕获分组和回溯引用
>小括号`()`匹配到的子表达式会被缓存为一个个组,方便后面对其引用
**假设要获取`html`中的`h1`标签:**
### 在正则表达式中使用\n可以引用第n个捕获组
```js
const str5 = '<p>正则表达式</p><h1>正则表达式</h1><h2>正则表达式</h2>'
const reg7 = /<(h1)>.+?<\/\1>/;// \1引用第一个捕获组
console.log(str5.match(reg7));
/*
[
  '<h1>正则表达式</h1>',
  'h1',
  index: 12,
  input: '<p>正则表达式</p><h1>正则表达式</h1><h2>正则表达式</h2>',
  groups: undefined
]
```

### 在正则表达式外使用$n引用第n个捕获组(RegExp.$n);
```js
const str6 = 'abc';
const reg8 = /(abc)/;
RegExp.$1;//'abc'
const newStr2 = str6.replace(reg8,'$1$1');
console.log(newStr2);
```

## 非捕获分组和限定查找
>因为捕获组` () `会将每个捕获到的结果缓存下来以便引用，所以会造成内存使用增加。如果只是想用分组的原始功能，而不需要缓存，则可以使用非捕获分组`(?:)`
```js
const str7 = 'abc';
const reg9 = /(?:abc)/;
RegExp.$1 // ''
//非捕获分组还有 (?=)、(?<=)、(?!)、(?<!) ，他们比 (?:) 多了限定作用，即只匹配而不会被输出。
```

## 前向查找-前向查找是用来限制后缀的
>`(?=)`:即查找符合限定条件`(?=)`的前面的匹配项（输出内容不包括 `(?=)` 中的匹配项）
```js
const str8 = 'a.png b.jpg c.fig d.svg';
// 查找所有 边界开头的、 .svg 前面的 小写字母
const reg10 = /\b[a-z](?=.svg)/g;
console.log(str8.match(reg10));//['d']
```
>`(?!)`:即查找不符合限定条件`(?!)`的前面的匹配项（输出内容不包括 `(?!)` 中的匹配项）
```js
const str9 = 'a.png b.jpg c.fig d.svg';
// 查找所有边界开头的、 非.svg 前面的、 `.[a-z]{3}` 前面的 小写字母。
const reg11 = /\b[a-z](?!.svg)(?=\.[a-z]{3})/g;
console.log(str9.match(reg11));//[ 'a', 'b', 'c' ]
```

## 后向查找-后向查找是用来限制前缀的
>查找符合限定条件` (?<=) `的后面的匹配项（输出内容不包括` (?<=) `中的匹配项）
```js
const str10 = '1. 1111; 2. 2222; 3. 3333; 4. 4444。'
//查找所有序号后面的项
const reg12 = /(?<=\b[0-9]+\.\s).+?[; 。]/g;
console.log(str10.match(reg12));//[ '1111;', '2222;', '3333;', '4444。' ]
```
>查找 不符合 限定条件` (?<!) `的后面的匹配项（输出内容不包括` (?<!) `中的匹配项）
```js
const str11 = 'a.png b.jpg c.gif d.svg';
//查找前缀不为a b c的后面的项
const reg13 = /\b(?<![abc]\.)[a-z]{3}/g;
console.log(str11.match(reg13));//[ 'svg' ]
```

## 例子
### 前向查找和后向查找齐用
假设要获取` <img crossorigin src="https://abcdefg.com" data-img-url="https://test.com"> `中的` data-img-url `属性中的链接。可以确定的是链接左边一定是` data-img-url=" ，`右边一定是紧贴着` " `（非贪婪）
```js
const str12 = '<img crossorigin src="https://abcdefg.com" data-img-url="https://test.com">'
const dataImgUrl = 'data-img-url';
const reg14 = new RegExp(`(?<=${dataImgUrl}=").+?(?=")`, 'g');
console.log(str12.match(reg14));//[ 'https://test.com' ]
```
### 回溯引用和非贪婪并用
假如我要获取一段`html`中的文本，但是我又不想要加了 `not-show-in-text `标记的标签中的文本
```js
const notShowInText = 'not-show-in-text';
const html = `
  <p>test1</p>
  <p ${notShowInText} style="text-align: center;">
    <b>表1.1asdfsdf测试</b>
  </p>
  <p>text2</p>
`
const reg15 = new RegExp(`<([a-z][a-z1-6]*?)[^>]+${notShowInText}[\\s\\S]+?</\\1>`, 'g')
const text = html.replace(reg15, '').replace(/<[^>]+>/g, '')
```
### replace第二个参数可以是回调函数
比如，想把` yyyy-mm-dd `格式，替换成` mm/dd/yyyy `怎么做？
```js
const regex = /(\d{4})-(\d{2})-(\d{2})/;
const string = "2017-06-20";
const result = string.replace(regex, "$2/$3/$1");//在正则表达式外使用$n引用第n个捕获组
console.log(result);// "06/12/2017"
// /其中 replace 第二个参数里用$1、$2、$3指代相应的分组
```

**等价于如下的形式：**
```js
const result2 = string.replace(regex, function() {
  return RegExp.$2 + "/" + RegExp.$3 + "/" + RegExp.$1
});
console.log(result2);// "06/12/2017"
```

**也等价于：**
```js
const result3 = string.replace(regex, function(match, year, month, day) {
  return month + "/" + day + "/" + year;
});
console.log(result3);// "06/12/2017"
```