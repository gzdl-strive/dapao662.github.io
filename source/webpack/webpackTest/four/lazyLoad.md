---
title: lazyLoad(懒加载)
date: 2020-11-03 21:08:24
tag: webpack
---

```js
//index.js
console.log('index.js');

//import { mul } from './test';

document.getElementById('btn').onclick = function() {
    //懒加载~:当文件需要使用时才加载
    //预加载webpackPrefetch:true-->会在使用之前，提前加载js文件
    //正常加载可以认为是并行加载（同一时间加载多个文件）
    //预加载prefetch：等其他资源加载完毕，浏览器空闲了，再偷偷加载资源 
    import(/*webpackChunkName: 'test', webpackPrefetch: true*/'./test').then(({mul})=>{
        console.log(mul(4,5));
    });
}
```

```js
//webpack.config.js
const { resolve } = require("path");
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    //单入口
    entry:"./src/js/index.js",
    output:{
        //[name]:取文件名
        filename:'js/[name].[contenthash:10].js',
        path:resolve(__dirname,'build')
    },
    plugins:[
        new HtmlWebpackPlugin({
            template:'./src/index.html',
            minify:{
                collapseWhitespace:true,
                removeComments:true
            }
        })
    ],
    /*
        可以将node_modules中代码单独打包成一个chunk最终输出
    */
    optimization: {
        splitChunks: {
            chunks: 'all'
        }
    },
    mode:'production'
}
```