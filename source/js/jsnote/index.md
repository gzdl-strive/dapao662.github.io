---
title: js笔记
date: 2021-01-22 14:52:30
tag: js
---

## 浏览器打开新窗口，当关闭窗口时刷新原窗口
```js
// a.html
window.open('http://www.baidu.com');
//当新窗口baidu关闭时->刷新原窗口
window.opener.location.reload(); self.close();
```