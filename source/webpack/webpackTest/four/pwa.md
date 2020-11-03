---
title: pwa渐进式网络开发应用程序（离线可访问）
date: 2020-11-03 21:31:24
tag: webpack
---

> PWA:渐进式网络开发应用程序（离线可访问）workbox --> workbox-webpack-plugin 下载workbox-webpack-plugin

```js
const { resolve } = require("path");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const WorkboxWebpackPlugin = require('workbox-webpack-plugin');

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
        }),
        //pwa
        new WorkboxWebpackPlugin.GenerateSW({
            /*
                1、帮助serviceworker快速启动
                2、删除旧的serviceworker
                生成一个serviceworker配置文件
            */
            clientsClaim:true,
            skipWaiting:true
        })
    ],
    mode:'production',
    devtool:'source-map'
}
```

**index.js**
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

/*
  1、eslint不认识 window、navigator全局变量
   解决：需要修改package.json中eslintConfig配置
    "env": {
      "browser": true //支持浏览器端全局变量
    }
  2、sw代码必须运行在服务器上
    --> nodejs
    -->
      npm i serve -g
      serve -s build 启动服务器，将build目录下所有资源作为静态资源暴露出去

*/

// 注册serviceworker
// 处理兼容性问题
// if ('serviceWorker' in navigator) {
//   window.addEventListener('load', () => {
//     navigator.serviceWorker.register('/service-worker.js')
//       .then(() => {
//         console.log('sw注册成功');
//       })
//       .catch(() => {
//         console.log('注册失败');
//       });
//   });
// }
```
