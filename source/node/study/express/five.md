---
title: express加载静态资源文件
date: 2020-11-23 20:43:19
tag: nodejs
---

```js
const express = require('express');
const app = express();

//中间件
app.use(express.static('static'));

//express加载静态资源文件
app.get('/index', (req, res) => {
    res.sendFile(`${__dirname}/html/index2.html`);
});

app.listen(3000);
```

**index2.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>express加载静态资源</title>
    <link rel="stylesheet" href="/css/style.css">
</head>
<b>
    <h1>Express加载静态资源</h1>
    <img src="/img/yellow.jpg" alt="燃料喷射器">
    <script src="/js/public.js"></script>
</body>
</html>
<!-- 这些文件都存于static目录下 -->
```