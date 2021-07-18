---
title: 环境搭建
date: 2021-07-14 10:38:24
toc: true
---

>1、创建一个空文件夹，命名为`Monitor`
>2、在终端中使用`npm init -y`初始化该文件夹，自动生成`package.json`文件
>3、 安装将会用到的插件终端中执行
```js
npm install webpack webpack-cli user-agent html-webpack-plugin webpack-dev-server -D 
```
**注意：webpack-cli插件可能会有版本问题，这边我使用的各插件版本如下**
![1](/assets/monitorImg/dep.png "依赖")
简单介绍这几个插件的功能，webpack和webpack-cli是用于配置webpack的，webpack-dev-server是用于构建开发服务器的，html-webpack-plugin是自动打包出HTML文件的插件, user-agent把浏览器的UserAgent变成一个对象

>4、在当前文件夹下新建src目录和dist目录，以及`webpack.config.js`文件,在src目录下创建index.js和index.html文件

>5、编写`webpack.config.js`
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');//webpack打包项目的HtmlWebpackPlugin生成产出HTML文件

module.exports = {
  entry: './src/index.js',//入口文件
  context: process.cwd(),//上下文目录
  mode: 'development',//开发环境
  output: {
    path: path.resolve(__dirname, 'dist'),//输出目录为dist文件夹下
    filename: 'monitor.js',//输出文件名
  },
  devServer: {
    contentBase: path.resolve(__dirname, 'dist'),//devServer静态文件根目录
  },
  plugins: [
    new HtmlWebpackPlugin({
      //自动打包出HTML文件的插件
      template: './src/index.html',
      inject: 'head'
    })
  ]
}
```

>6、因为已经配置好了`webpack.config.js`文件(包含dev-server)，所以修改package.json文件的scripts对象以便后序启动和调试
```js
"script" : {
  "build": "webapck",
  "dev": "webpack-dev-server"
}
```
