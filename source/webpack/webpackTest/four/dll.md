---
title: dll
date: 2020-11-03 21:37:24
tag: webpack
---

```js
//webpack.config.js
const {resolve} = require('path');
const HtmlWbpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');

module.exports={
    entry:'./src/index.js',
    output:{
        filename:'built.js',
        path:resolve(__dirname,'build')
    },
    module:{
        rules:[
            //loader的配置
        ]
    },
    plugins:[
        new HtmlWbpackPlugin({
            template:'./src/index.html'
        }),
        //告诉webpack那些库不参与打包，同时使用时的名称也得变~
        new webpack.DllReferencePlugin({
            manifest: resolve(__dirname,'dll/manifest.json')
        }),
        //将某个文件打包输出去,并在html中通过自动引入该文件

    ],
    mode:'production'
}
```

```js
//webpack.dll.js
/*
    使用dll技术，对某些库（第三方库：jquery、react、vue。。。）进行单独打包
        当你运行webpack时，默认查找webpack.config.js配置文件
        需求：需要运行webpack.dll.js文件
            -->webpack --config webpack.dll.js
*/
const { resolve } = require('path');
const webpack = require('webpack');

module.exports = {
    entry: {
        //最终打包生成的[name]-->jquery
        //['jquery']-->要打包的库是jquery
        jquery: ['jquery']
    },
    output: {
        filename: '[name].js',
        path:resolve(__dirname,'dll'),
        library: '[name]_[hash]', //打包的库里面向外暴露出去的内容叫什么名字
    },
    plugins: [
        //打包生成一个manifest.json --> 提供和jquery映射
        new webpack.DllPlugin({
            name: '[name]_[hash]',//映射库的暴露的内容名称
            path: resolve(__dirname,'dll/manifest.json')//输出文件路径
        })
    ],
    mode: 'production'
}

```