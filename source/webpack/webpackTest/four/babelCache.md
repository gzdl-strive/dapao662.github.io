---
title: babel缓存与文件资源缓存
date: 2020-11-03 15:41:02
tag: webpack
---

## 缓存
### bable缓存:
    cacheDirectory:true-->让第二次打包构建速度更快
### 文件资源缓存:
* **hash**:每次`webpack`构建时会生成一个唯一的`hash`值
问题：因为`js`和`css`同时使用一个`hash`值。
如果重新打包，会导致所以缓存失效。（可能我却只改动一个文件）
* **chunkhash**:根据`chunk`生成的`Hash`值。如果打包来源于同一个`chunk`,那么`hash`值一样
问题:`js`和`css`的`hash`值还是一样的
因为`css`是在`js`中被引入的,所以同属于一个`chunk`
* **contenthash**:根据文件的内容生成`hash`值。不同文件`hash`值一定不一样-->**让代码上线运行缓存更好使用**

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
