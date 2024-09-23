---
title: redis缓存
date: 2020-11-15 17:19:44
---

**redis.js**
```js
//redis缓存
const redis = require("redis");
const { promisify } = require("util");

//链接redis服务
const client = redis.createClient(6379, 'localhost');

//promise化redis方法,以使用async/await
const getAsync = promisify(client.get).bind(client);
const setAsync = promisify(client.set).bind(client);

async function redisData() {
    //先获取缓存中数据，有数据就读缓存，没有缓存就去拉取girls数据
    let data = await getAsync("key");
    if (!data) {
        //拉接口
        const root = require("./girls.json");
        data = JSON.stringify(root);
        //设置缓存数据
        await setAsync("key", data);
        console.log("##### 将数据写入缓存 #####:", data);
        return data;
    }
    console.log('##### 从缓存中读取数据 #####:', data);
    return data;
}

async function main() {
    //将数据打入缓存
    await redisData();
    //从缓存中读取数据
    await redisData();
}

main();
```
>先通过getAsync来读取redis缓存中的数据，如果有数据，直接返回，绕过接口调用，如果没有数据，就会调用天秀接口，然后setAsync更新到缓存中，以便下次调用。因为redis存储的是字符串，所以在设置缓存的时候，需要加上JSON.stringify(data)

**将数据放在redis缓存里有几个好处**
* 可以实现多接口复用、多机共享缓存
* 这就是传说中的云备胎


作者：第一名的小蝌蚪
链接：https://juejin.im/post/6893286451711049742
来源：掘金