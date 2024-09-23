---
title: for循环
date: 2020-11-04 15:43:02
tag: js
---

```js
const bluebird =require('bluebird');
const fetch = require('node-fetch');

async function getZhihuColumn(id){
    await bluebird.delay(1000);
    const url=`https://zhuanlan.zhihu.com/api/columns/${id}`;
    const response =await fetch(url);
    return await response.json();
}

const showColumnInfo = async () => {
    console.time('aaa');

    const names = ['feweekly','toolingtips'];
    //循环串行--2439.979ms
    // for (const name of names){
    //     const column =await getZhihuColumn(name);
    //     console.log(`Id : ${column.id}`);
    //     console.log(`Intro : ${column.intro}`);
    // } 

    //循环并行--先执行异步再调用结果--1278.275ms
    const promises = names.map(x=>getZhihuColumn(x));
    for (const promise of promises){
        const column = await promise;
        console.log(`Id : ${column.id}`);
        console.log(`Intro : ${column.intro}`);
    }
    console.timeEnd('aaa');
}
showColumnInfo();

```