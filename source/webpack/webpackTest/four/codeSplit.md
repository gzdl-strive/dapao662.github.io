---
title: codeSplit(代码分割)
date: 2020-11-03 21:18:24
tag: webpack
---

## demo1
```js
const { resolve } = require("path");
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    //单入口
    /*
        entry: "./src/js/index.js"
    */
    entry: {
        //多入口：有一个入口，最终输出就有一个bundle
        main: "./src/js/index.js",
        test: "./src/js/test.js"
    },
    output: {
        //[name]:取文件名
        filename: "js/[name].[contenthash:10].js",
        path: resolve(__dirname, "build")
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: "./src/index.html",
            minify: {
                collapseWhitespace: true,
                removeComments: true
            }
        })
    ],
    mode: "production"
}
```

## demo2

```js
const { resolve } = require("path");
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    //单入口
    //entry:"./src/js/index.js",
    //多入口
    entry:{
        index:"./src/js/index.js",
        test:'./src/js/test.js'
    },
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
        自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk
    */
    optimization: {
        splitChunks: {
            chunks: 'all'
        }
    },
    mode:'production'
}
```

## demo3

```js
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