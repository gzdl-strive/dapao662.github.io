---
title: express子应用
date: 2020-11-23 20:49:19
tag: nodejs
---

```js
//子应用
const express = require('express');
const article = require('./routers/articleRouter');
const category = require('./routers/categoryRouter');
//一个主应用
const app = express();

/**
 * blog系统,文章的管理(article),文章分类的管理(category)
 */
//使用子应用--通过主应用调用子应用
app.use('/article', article);
app.use('/category', category);

app.listen(3000);
```

**articleRouter.js**
```js
const express = require('express');

//文章的子应用
const article = express();

//路由
article.get('/add', (req,res) => {
    console.log('添加文章');
});
article.get('/del', (req,res) => {
    console.log('删除文章');
});
article.get('/get', (req,res) => {
    console.log('显示文章');
});

module.exports = article;
```

**categoryRouter.js**
```js
const express = require('express');
//文章类目的子应用
const category = express();

category.get('/add', (req,res) => {
    console.log('添加文章类目');
});
category.get('/del', (req,res) => {
    console.log('删除文章类目');
});
category.get('/get', (req,res) => {
    console.log('显示文章类目');
});

module.exports = category;
```