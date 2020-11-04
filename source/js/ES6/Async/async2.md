---
title: async函数用在promisechain中
date: 2020-11-04 15:19:26
tag: js
---

```js
const fetch = require('node-fetch');

async function getZhihuColumn(id) {
    const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response = await fetch(url);
    return await response.json();
}
//光标放在getZhihuColumn上可以看到返回的是一个Promise函数
getZhihuColumn('feweekly')
    .then(column => {
        console.log(`Id: ${column.id}`);
        console.log(`Intro: ${column.intro}`);
    });
```