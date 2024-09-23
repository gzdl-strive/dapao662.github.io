---
title: 使用Promise.all()让多个await操作并行
date: 2020-11-04 15:39:02
tag: js
---

```js
const fetch = require('node-fetch');
const { resolve } = require('path');

const getZhihuColumn=async (id)=> {
    const url=`https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response = await fetch(url);
    return await response.json();
};

const showColumnInfo = async ()=>{
    console.time('aaa');
    //Promise.all返回一个promise--可以用await--异步操作并行--1277.974ms
    const [feweekly,toolingtips] = await Promise.all([
        getZhihuColumn('feweekly'),
        getZhihuColumn('toolingtips'),
    ])

    console.log(`Id: ${feweekly.id}`);
    console.log(`Intro: ${feweekly.intro}`);  

    console.log(`Id: ${toolingtips.id}`);
    console.log(`Intro: ${toolingtips.intro}`);    

    console.timeEnd('aaa');
}
showColumnInfo();
```