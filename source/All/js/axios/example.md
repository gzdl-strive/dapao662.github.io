---
title: axios-创建实例对象
date: 2021-11-09 10:14:24
toc: true
---

**js**

```js
//创建实例对象
const duanzi = axios.create({
  baseURL: 'https://api.apiopen.top',
  timeout: 2000
});

//这里  duanzi 与 axios 对象的功能几近是一样的
duanzi({
  url: '/getJoke',
}).then(response => {
  console.log(response);
});

duanzi.get('/getJoke').then(response => {
  console.log(response.data);
})
```
