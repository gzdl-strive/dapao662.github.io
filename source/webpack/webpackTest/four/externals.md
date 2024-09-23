---
title: externals
date: 2020-11-03 21:35:24
tag: webpack
---

```js
const {resolve} = require('path');
const HtmlWbpackPlugin = require('html-webpack-plugin');

module.exports={
    entry:'./src/js/index.js',
    output:{
        filename:'js/built.js',
        path:resolve(__dirname,'build')
    },
    plugins:[
        new HtmlWbpackPlugin({
            template:'./src/index.html'
        })
    ],
    mode:'production',
    externals: {
        //忽略库名 -- npm 包名
        //拒绝jQuery被打包进来
        jquery: 'jQuery'
    }
}
```