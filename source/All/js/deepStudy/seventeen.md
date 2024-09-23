---
title: 模板字符串实现
date: 2022-07-30 22:02:02
tag: js
---

### 模板字符串实现
```js
function templateStr(str) {
  return str.replace(/\$\{(.*?)\}/g, function (match, p1, offset, string)) {
    return eval(p1);
  }
}
```

### 双括号模板字符串实现
```js
function templateStr2(str, obj) {
  const pattern = /\{\{(.*?)\}\}/g;
  if (pattern.test(str)) {
    return str.replace(pattern, function (match, p1, offset, string)) {
      const trimStr = p1.trim();
      return obj[trimStr];
    }
  }
  return str;
}
```