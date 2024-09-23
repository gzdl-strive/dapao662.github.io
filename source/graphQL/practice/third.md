---
title: schedule
date: 2020-11-15 17:21:44
---

轮询更新 -> schedule
女神的备胎用久了，会定时换一批备胎，让新鲜血液进来，发现新的快乐
缓存也一样，需要定时更新，保持与数据源的一致性，代码如下：

**schedule.js**
```js
const schedule = require('node-schedule');
const { promisify } = require("util");
const redis = require("redis");
const client = redis.createClient(6379, '127.0.0.1');
// promise化
const setAsync = promisify(client.set).bind(client);

// 每个小时更新一次缓存
schedule.scheduleJob('* * 0 * * *', async () => {
    const data = require('./girls.json');
    // 设置redis缓存数据
    await setAsync("缓存", JSON.stringify(data));
});

console.log('每个小时更新一次缓存');

```
天秀接口不是一个强实时性接口，数据源一周可能才会变一次
所以我们根据实际情况用轮询来设置更新缓存频率
我们用node-schedule这个库来轮询更新缓存，* * 0 * * *这个的意思就是设置每个小时的第0分钟就开始执行缓存更新逻辑，将获取到的数据更新到缓存中，这样其他接口和机器在调用缓存的时候，就能获取到最新数据，这就是共享缓存和轮询更新的好处。

作者：第一名的小蝌蚪
链接：https://juejin.im/post/6893286451711049742
来源：掘金
