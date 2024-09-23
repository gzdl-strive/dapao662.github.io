---
title: 正确处理多个await操作的并行串行
date: 2020-11-04 15:34:02
tag: js
---

```js
const fetch = require('node-fetch');
const { resolve } = require('path');

const sleep = (timeout) => new Promise(
    resolve => {
        setTimeout(resolve, timeout);
    }
);

const getZhihuColumn = async (id) => {
    await sleep(2000);
    const url = `https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response = await fetch(url);
    return await response.json();
};

const showColumnInfo = async () => {
    console.time('aaa');
    //使用await使得使得函数串行--4535.213ms
    const feweekly = await getZhihuColumn('feweekly');
    const toolingtips = await getZhihuColumn('toolingtips');

    //并行--2286.258ms--先执行异步操作再调用它的结果 
    // const feweeklyPromise = getZhihuColumn('feweekly');
    // const toolingtipsPromise = getZhihuColumn('toolingtips');
    // const feweekly = await feweeklyPromise;
    // const toolingtips = await toolingtipsPromise;


    console.log(`Id: ${feweekly.id}`);
    console.log(`Intro: ${feweekly.intro}`);

    console.log(`Id: ${toolingtips.id}`);
    console.log(`Intro: ${toolingtips.intro}`);

    console.timeEnd('aaa');
}
showColumnInfo();
```