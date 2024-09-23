---
title: graphQL基本使用实例
date: 2020-11-15 16:39:24
---

> 如何从一百多个字段中，抽取任意n个字段，这就用到graphQL

1、定义数据池 root
2、描述数据池中数据结构 schema
3、自定义查询数据 query

**girls.json**

``` json
{
    "girls": [{
        "id": 1,
        "name": "女神一",
        "iphone": 12345678901,
        "weixin": "cscscscs",
        "height": 175,
        "school": "蓝翔",
        "wheel": [{ "name": "备胎一号", "money": "50万元" }, { "name": "备胎二号", "money": "30万元" }]
    },{
        "id": 2,
        "name": "女神二",
        "iphone": 98765432101,
        "weixin": "gogogogo",
        "height": 170,
        "school": "新东方",
        "wheel": [{ "name": "备胎三号", "money": "35万元" }, { "name": "备胎四号", "money": "25万元" }]
    }]
}
```

**graphql.js**

``` js
const {
    graphql,
    buildSchema
} = require("graphql");

//graphQL按需加载数据
async function getDataGraphQL() {
    //数据池
    const root = require("./girls.json");
    //schema
    const schema = buildSchema(`
        type Wheel {
            name: String,
            money: String
        }
        type Info {
            id: Int, 
            name: String,
            iphone: Int,
            weixin: String,
            height: Int,
            school: String,
            wheel: [Wheel]
        }
        type Query {
            girls: [Info]
        }
    `);
    const query = `
        {
            girls {
                name
                wheel {
                    money
                }
            }
        }
    `;
    //查询
    const resData = await graphql(schema, query, root);
    //JSON.stringify把js对象解析成JSON对象
    console.log("按需加载后的数据", JSON.stringify(resData));
    return resData;
}

getDataGraphQL();
```

**node graphql.js后输出结果为：**

``` js
按需加载后的数据 {
    "data": {
        "girls": [{
            "name": "女神一",
            "wheel": [{
                "money": "50万元"
            }, {
                "money": "30万元"
            }]
        }, {
            "name": "女神二",
            "wheel": [{
                "money": "35万元"
            }, {
                "money": "25万元"
            }]
        }]
    }
}
```
作者：第一名的小蝌蚪
链接：https://juejin.im/post/6893286451711049742
来源：掘金