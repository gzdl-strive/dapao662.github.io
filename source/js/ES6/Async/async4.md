---
title: 处理async函数中的错误
date: 2020-11-04 15:28:02
tag: js
---

```js
const fetch = require('node-fetch');

const getZhihuColumn = async (id) => {
    const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response = await fetch(url);
    console.log({status: response.status});//{status:404}
    if (response.status != 200) {
        throw new Error(response.statusText);
    }
    return await response.json();
}

const showColumnInfo = async (id) => {
    try {
        const column = await getZhihuColumn(id);
        console.log(`Id: ${column.id}`);
        console.log(`Intro: ${column.intro}`); 
    } catch(e) {
        console.log(e);
    }
}
showColumnInfo('feweekly123');
```
