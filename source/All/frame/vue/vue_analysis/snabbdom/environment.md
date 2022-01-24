---
title: snabbdom简介和搭建环境
date: 2022-01-09 11:03:24
toc: true
---

>`snabbdom`是瑞典语单词，单词原意**是速度**
>`snabbdom`是著名的虚拟DOM库，是diff算法的鼻祖，Vue源码借鉴了snabbdom;
>官方github地址：[snabbdom](https://github.com/snabbdom/snabbdom "snabbdom")

## 环境搭建
>`snabbdom`库是DOM库，当然不能在`nodejs`环境运行，所以我们需要搭建`webpack`和`webpack-dev-server`开发环境，好消息是不需要安装任何`loader`,我们还需要`html-webpack-plugin`插件的使用

**webpack配置文件内容如下**
```js
//webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    clean: true
  },
  devtool: 'inline-source-map',
  devServer: {
    static: path.resolve(__dirname, 'dist'),
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './index.html',
      inject: 'body',
      scriptLoading: "blocking",
      favicon: './favicon.ico'
    })
  ]
}
```

**不要忘记`在index.html`中放置一个`div#container`**