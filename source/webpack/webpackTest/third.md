---
title: webpack生产环境配置
date: 2020-11-03 14:35:16
tag: webpack
---


```js
const { resolve } = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');//提取css为单独文件插件
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')//压缩css插件
const HtmlWebpackPlugin = require('html-webpack-plugin');//压缩html代码

//定义nodejs环境变量：决定使用broserslist的哪个环境
process.env.NODE_ENV = 'production';

//复用Loader
const commonCssLoader = [
    //这个loader取代style-loader.作用:提取js中的css成单独文件
    MiniCssExtractPlugin.loader,
    //将css文件整合到js文件中
    'css-loader',
    /*
        css兼容性处理:postcss-->下载这两包postcss-loader postcss-preset-env
        帮postcss找到package.json中browserslist里面的配置，通过配置加载指定的css兼容性样式
            "browserslist":{
                //开发环境--》设置环node境变量：process.env.NODE_ENV = 'development'
            "development":[
            "last 1 chrome version",
            "last 1 firefox version",
            "last 1 safari version"
            ],
            //生产环境：默认是看生产环境
            "production":[
            ">0.2%",
            "not dead",
            "not op_mini all"
            ]
        }
    */
    {
        //还需要在package.json中定义browserslist
        loader:'postcss-loader',
        options:{
            ident: 'postcss',
            plugins: ()=>[
                //postcss的插件
                require('postcss-preset-env')()
            ]
        }
    }
];

module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [...commonCssLoader]
            },
            {
                test: /\.less$/,
                use: [...commonCssLoader, 'less-loader']
            },
            /*
                正常来说，一个文件只能被一个Loader处理
                当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序:
                    先执行eslint 在执行babel
            */
            {
                /*
                    js语法检查——  eslint-loader  eslint
                    下载eslint eslint-loader
                    注意：只检查自己写的源代码，第三方库是不用检查的
                    设置检查规则:
                        package.json中eslintConfig中设置
                          "eslintConfig": {
                                "extends": "airbnb-base",
                                "env": {
                                "browser": true
                                }
                            },
                        airbnb-->eslint-config-airbnb-base  eslint  eslint-plugin-import
                */
                test: /\.js$/,
                //不要检查第三方库，否则会报错
                exclude: /node_modules/,
                loader: 'eslint-loader',
                //优先执行
                enforce: 'pre',
                options: {
                    //自动修复eslint的错误
                    fix: true
                }
            },
            //IE浏览器不能识别ES6语法
            /*
            js兼容性处理:babel-loader @babel/core @bebel/preset-env
                1、基本js兼容性处理-->@babel/preset-env
                    问题：只能转换基本语法，如：不能转换promise高级语法
                2、全局js兼容性处理-->@babel/polyfill
                    直接在index.js中import就行
                    问题：（体积太大）我只要解决部分兼容性问题，但是将所有兼容性代码全部引入
                3、需要做兼容性处理的就做：按需加载 --> core-js(把js中的import注释)
            */
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
                options: {
                    //预设:指示babel做怎么样的兼容性处理
                    presets: [
                        [
                            '@babel/preset-env',
                            {
                                //按需加载
                                useBuiltIns: 'usage',
                                //指定core-js版本
                                corejs:{version:3},
                                //指定兼容性做到哪个版本浏览器
                                targets: {
                                    chrome:'60',
                                    firefox:'50'
                                }
                            }
                        ] 
                    ]
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
    },
    plugins: [
        new MiniCssExtractPlugin({
            //对输出的css文件进行重命名
            filename: 'css/built.css',
        }),
        //压缩css
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: './src/index.html',
            //压缩html代码
            minify: {
                //移除空格
                collapseWhitespace: true,
                //移除注释
                removeComments: true
            }
        })
    ],
    //生产环境下会自动压缩js代码
    mode: 'production'
}

```