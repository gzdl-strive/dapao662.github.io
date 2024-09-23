---
title: Async练习一
date: 2020-11-04 15:09:26
tag: js
---

### 之前调用接口方式
```js
//使用node-fetch库获取接口数据
const fetch = require('node-fetch');

function getZhihuColumn(id) {
    const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
    fetch(url)
        .then(response => response.json())
        .then(column => {
            console.log(`ID: ${column.id}`);
            console.log(`Intro: ${column.intro}`);
        });
}
getZhihuColumn('feweekly');
```

### 使用async/await
```js
const fetch = require('node-fetch');
async function getZhihuColumn(id) {
    const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response = await fetch(url);
    const column = await response.json();
    console.log(`ID: ${column.id}`);
    console.log(`Intro: ${column.intro}`);
}
getZhihuColumn('feweekly');
```