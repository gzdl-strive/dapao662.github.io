---
title: react换行
date: 2020-12-24 20:32:10
tag: react
---

>问题描述：无法匹配接口返回的\n字符，不能自动换行

## 解决方法

### 方法一、
```js
// 如果是\n使用正则匹配之后替换为<br />
// text为变量
<div dangerouslySetInnerHTML={{__html:String(text).replace(/[\r\n]/g, '<br/>')}}></div>
// 如果直接是<br />则不用使用正则匹配
```

### 方法二、
**在父节点中添加css属性 whiteSpace: 'pre-wrap'**
**然后下面任选一种**
* 字符串中使用\n添加换行符
* 使用``包裹字符串
```js
<div style={{whiteSpace: 'pre-wrap'}}>{text}</div>
```
