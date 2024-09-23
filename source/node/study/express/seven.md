---
title: express使用ejs模板引擎
date: 2020-11-23 20:49:19
tag: nodejs
---

```js
const express = require('express');
const app = express();

//使用ejs模板引擎

//设置模板引擎类型
app.set('view engine','ejs');
//设置模板引擎的目录
app.set('views',`${__dirname}/views`);

app.get('/',(req,res) => {
    res.render('header',{msg: 'hello express ejs'})
})

app.listen(3000);

```

**views/header.ejs**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ejs</title>
</head>
<body>
    <h1><%=msg%></h1>
</body>
</html>
```