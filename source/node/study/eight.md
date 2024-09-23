---
title: mysql
date: 2020-11-23 20:32:19
tag: nodejs
---

>npm install mysql --save
```js
//引入mysql模块
const mysql = require('mysql');

//创建数据库连接对象
const connection = mysql.createConnection({
    host: '127.0.0.1',
    user: 'root',
    password: 'root',
    database: 'blog',
    port: 3306,//3306不用设置
});

//连接数据库
connection.connect();
console.log('数据库连接成功');

//创建SQL语句--查询
let sql = 'SELECT id,username,password FROM user';
connection.query(sql,(err, results) => {
    if (err) {
        console.log(err.message);
        return;
    }
    console.log(results);//[ RowDataPacket { id: 1, username: 'admin', password: '123456' } ]
    results.map(item => {
        console.log(item.id,item.username,item.password);
    })
});

//关闭数据库
connection.end();
console.log('数据库关闭成功');
```
