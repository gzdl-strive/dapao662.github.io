---
title: 把任意类型的函数转成async函数
date: 2020-11-04 15:23:02
tag: js
---
```js
const fetch = require('node-fetch');

const getZhihuColumn = async (id) => {
    const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response = await fetch(url);
    return await response.json();
};

//在class中使用async函数
class APIClient{
    async getColumn(id) {
        const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
        const response = await fetch(url);
        return await response.json();
    }
}

(async () => {
    const client = new APIClient();
    cosnt column = await client.getColumn('feweekly');
    //const column = await getZhihuColumn('feweekly');
    console.log(`Id: ${column.id}`);
    console.log(`Intro: ${column.intro}`);
})

```
