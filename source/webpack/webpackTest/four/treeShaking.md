---
title: treeShaking
date: 2020-11-03 21:11:24
tag: webpack
---

## tree shaking 去除无用代码
>前提条件：1、必须使用ES6模块化 2、开启production环境 **作用：减少代码体积**

    在package.json中配置
        "sideEffects":false 所以代码都没有副作用（都可以进行tree shaking）
        问题：可能会把css/@babel/polyfill(副作用)文件干掉
        解决："sideEffects":["*.css","*.less"]

```js
const { resolve } = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');

//定义nodejs环境变量：绝对使用broserslist的哪个环境
process.env.NODE_ENV = 'production';

//复用Loader 
const commonCssLoader = [
    MiniCssExtractPlugin.loader,
    'css-loader',
    {
        //还需要在package.json中定义browserslist
        loader:'postcss-loader',
        options:{
            ident: 'postcss',
            plugins: ()=>[
                require('postcss-preset-env')()
            ]
        }
    }
];

module.exports = {
    entry:'./src/js/index.js',
    output:{
        filename:'js/built.[contenthash:10].js',
        path:resolve(__dirname,'build')
    },
    module:{
        rules:[
            {
                //在package.json中eslintConfig中--》airbnb规则
                test:/\.js$/,
                exclude:/node_modules/,
                loader:'eslint-loader',
                //优先执行
                enforce:'pre',
                options:{
                    fix:true
                }
            },
            {
                //以下loader只会匹配一个
                //注意：不能有两个配置处理同一种类型的文件
                //提升构建速度
                oneOf:[
                    {
                        test:/\.css$/,
                        use:[...commonCssLoader]
                    },
                    {
                        test:/\.less$/,
                        use:[...commonCssLoader,'less-loader'],
                    },
                    /*
                    正常来说，一个文件只能被一个Loader处理
                    当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序:
                        先执行eslint 在执行babel
                    */
                    {
                        test:/\.js$/,
                        exclude:/node_modules/,
                        loader:'babel-loader',
                        options:{
                            presets: [
                                [
                                    '@babel/preset-env',
                                    {
                                        useBuiltIns: 'usage',
                                        corejs:{version:3},
                                        targets: {
                                            chrome:'60',
                                            firefox:'50'
                                        }
                                    }
                                ] 
                            ],
                            //开启babel缓存
                            //第二次构建时，会读取之前的缓存
                            cacheDirectory:true
                        }
                    },
                    {
                        test:/\.(jpg|png|gif)$/,
                        loader:'url-loader',
                        options:{
                            limit:8*1024,
                            name:'[hash:10].[ext]',
                            outputPath:'imgs'
                        }
                    },
                    {
                        test:/\.html$/,
                        loader:'html-loader'
                    },
                    {
                        exclude:/\.(js|css|less|html|jpg|png|gif)/,
                        loader:'file-loader',
                        options:{
                            outputPaht:'media'
                        }
                    }
                ]
            }
        ]
    },
    plugins:[
        new MiniCssExtractPlugin({
            filename:'css/built.[contenthash:10].css',

        }),
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
            template:'./src/index.html',
            minify:{
                collapseWhitespace:true,
                removeComments:true
            }
        })
    ],
    mode:'production',
    devtool:'source-map'
}
```

**index.js文件**
```js
import '../css/index.css';
import { mul } from './test';

function sum(...args) {
  return args.reduce((p, c) => p + c, 0);
}

// eslint-disable-next-line
console.log(sum(1, 2, 3, 4));

// eslint-disable-next-line
console.log(mul(5,6));
```