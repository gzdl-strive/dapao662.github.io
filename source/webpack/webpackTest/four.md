---
title: webpack优化环境配置
date: 2020-11-03 15:07:24
tag: webpack
---

# webpack 性能优化
* 开发环境性能优化
* 生产环境性能优化

## 开发环境性能优化
* 优化打包构建速度
    * [HMR](/webpack/webpackTest/four/hmr "hmr") 热模块替换--一个模块发生变化，只会重新打包这一个模块(`css:style-loader`已经配置,js:自己写)
* 优化代码调试功能
    * [source-map](/webpack/webpackTest/four/sourceMap "sourceMap")提供源代码到构建后代码映射技术

## 生产环境性能优化
* 优化打包构建速度
    * [oneOf](/webpack/webpackTest/four/oneOf "oneOf")
    * [babel缓存](/webpack/webpackTest/four/babelCache "babel缓存"):让第二次打包构建速度更快
    * 多进程打包
* 优化代码运行的性能
    * [缓存(hash-chunkhash-contenthash)](/webpack/webpackTest/four/babelCache "文件资源缓存")
    * [tree shaking](/webpack/webpackTest/four/treeShaking "treeShaking")去除无用代码(1、ES6模块化 2、production)
    * [code split](/webpack/webpackTest/four/codeSplit "codeSplit")
    * [懒加载/预加载](/webpack/webpackTest/four/lazyLoad "lazyLoad")
    * [PWA](/webpack/webpackTest/four/pwa "pwa")
    * [externals](/webpack/webpackTest/four/externals "externals")
    * [dll](/webpack/webpackTest/four/dll "dll")


