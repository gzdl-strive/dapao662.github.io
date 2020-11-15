---
title: Webpack优化
date: 2020-11-14 21:20:10
tag: optimization
toc: true
---
介绍了渲染优化，现在来看看webpack优化，自己平常写demo给团队做培训的时候都是自己手写webpack配置，虽然也就几十行，但每次都能让我巩固webpack的基本配置，下面直接说一下webpack优化手段有哪些
## 基础配置优化
* extensions
这个配置是属于resolve里面的，经常用来对文件后缀进行扩展,写法如下
```js
resolve: {
    extensions: ['.ts', '.tsx', '.js']
}
```
这个配置表示webpack会根据extensions去寻找文件后缀名，所以如果我们的项目主要用ts写的话，那我们就可以.tsx和.ts写前面，目的是为了让webpack能够快速解析
* alias
这个配置也是属于resolve里面的，是用来映射路径，能减少打包时间的主要原因是能够让webpack快速的解析文件路径，·找到对应的文件，配置如下
```js
resolve: {
  alias: {
    Components: path.resolve(__dirname, './src/components')
  }
}
```
* noParse
noParse表示不需要解析的文件，有的文件可能是来自第三方的文件，被 providePlugin引入作为windows上的变量来使用，这样的文件相对比较大，并且已经是被打包过的，所以把这种文件排除在外是很有必要的，配置如下
```js
module: {
  noParse: [/proj4\.js/]
}
```
* exclude
某些loader会有这样一个属性，目的是指定loader作用的范围，exclude表示排除某些文件不需要babel-loader处理，loader的作用范围小了，打包速度自然就快了，用babel-loader举一个简单例子
```js
{
    test: /\.js$/,
    loader: "babel-loader",
    exclude: path.resolve(__dirname, 'node_modules')
}
```
* devtool
这个配置是一个调试项，不同的配置展示效果不一样，打包大小和打包速度也不一样，比如开发环境下cheap-source-map肯定比source-map快，至于为什么，强烈推荐自己之前写的这一篇讲解devtool的文章：webpack devtools篇讲的非常详细。也可以看看[本博客的webpack的devtools](/webpack/webpackTest/four/sourceMap)
```js
{
    devtool: 'cheap-source-map'
}
```
* eslintignore
这个虽不是webpack配置但是对打包速度优化还是很有用的，在我的实践中eslint检查对打包的速度影响很大，但是很多情况我们不能没有这个eslint检查，eslint检查如果仅仅在vs里面开启的话，可能不怎么保险
因为有可能你vs中的eslint插件突然关闭了或者某些原因vs不能检查了，只能靠webpack构建去帮你拦住错误代码的提交，即使这样还不能确保万无一失，因为你可能某一次提交代码很急没有启动服务，直接盲改提交上去了。这个时候只能通过最后一道屏障给你保护，就是在CI的时候。比如我们也会是在jenkins构建的时候帮你进行eslint检查，三道屏障确保了我们最终出的镜像是不会有问题的。
所以eslint是很重要的，不能删掉，在不能删掉的情况下怎么让检查的时间更少了，我们就可以通过忽略文件，让不必要的文件禁止eslint,只对需要的文件eslint可以很大程度提高打包速度

## loader plugins优化
上述说了几个基础配置优化，应该还有其他的基础配置，今后遇到了再继续添加，现在在来讲讲利用某些loader,plugins来提高打包速度的例子
* cache-loader
这个loader就是在第一次打包的时候会缓存打包的结果，在第二次打包的时候就会直接读取缓存的内容，从而提高打包效率。但是也需要合理利用，我们要记住一点你加的每一个loader，plugins都会带来额外的打包时间。这个额外时间比他带来的减少时间多，那么一味的增加这个loader就没意义，所以cache-loader最好用在耗时比较大的loader上，配置如下
```js
{
  rules: [
    {
      test: /\.vue$/,
      use: [
        'cache-loader',
        'vue-loader'
      ],
      include: path.resolve(__dirname, './src')
    }
  ]
}
```
* webpack-parallel-uglify-plugin, uglifyjs-webpack-plugin, terser-webpack-plugin
在上面的渲染优化中我们已经知道，文件越小渲染的速度是越快的。所以我们在配置webpack时候经常会用到压缩，但是压缩也是需要消耗时间的，所以我们我们经常会用到上面三个插件之一来开启并行压缩，减少压缩时间，我们用webpack4推荐使用的terse-webpack-plugin做例子来说明
```js
optimization: {
  minimizer: [
      new TerserPlugin({
        parallel: true,
        cache: true
      })
    ],
}
```
* happypack, parallel-webpack, thread-loader
这几个loader/plugin和上面一样也是开启并行的，只不过是开启并行构建。由于happypack的作者说自己的兴趣已经不再js上了，所以已经没有维护了，并推荐如果使用的是webpack4的话，就去使用thread-loader。基本配置如下
```js
{
test: /\.js$/,
use: [
    {
    loader: "thread-loader",
    options: threadLoaderOptions
    },
    "babel-loader",
],
exclude: /node_modules/,
}
```
* DllPlugin,webpack.DllReferencePlugin
上面说的几个并行插件理论上是可以增加构建速度，网上很多文章都是这么说的，但是我在实际的过程中使用，发现有时候不仅没提升反而还降低了打包速度，网速查阅给的理由是可能你的电脑核数本来就低，或者当时你CPU运行已经很高了，再去开启多进程导致构建速度降低。
上面说的几个并行插件可能在某些情况下达不到你想要的效果，然而在我们团队优化webpack性能经验来看，这次所说的两个插件是很明显并且每次都能提高打包速度的。原理就是先把第三方依赖先打包一次生成一个js文件，然后真正打包项目代码时候，会根据映射文件直接从打包出来的js文件获取所需要的对象，而不用再去打包第三方文件。只不过这种情况打包配置稍微麻烦点，需要写一个webpack.dll.js。大致如下
**webpack.dll.js**
```js
const path = require('path');
const webpack = require('webpack');

module.exports = {
    entry: {
        library: ["vue", "moment"]
    },

    output: {
        filename: '[name].dll.js',
        path: path.resolve(__dirname, 'json-dll'),
        library: '[name]'
    },

    plugins: [
        new webpack.DllPlugin({
            path: './json-dll/library.json',
            name: '[name].json'
        })
    ]
}
```
**webpack.dev.js**
```js
  new AddAssetHtmlWebpack({
     filepath: path.resolve(__dirname, './json-dll/library.dll.js')
  }),

 new webpack.DllReferencePlugin({
      manifest: require("./json-dll/library.json")
    })

```

## 其他优化配置
* webpack-bundle-analyzer
这个插件可以用可视化帮我们分析打包体积，从而来采用合适的优化方式去改进我们的webpack配置
* speed-measure-webpack-plugin
这个插件可以告诉我们打包时候每一个loader或者plugin花费了多少时间，从而对耗时比较长的plugin和loader做优化
* friendly-errors-webpack-plugin
这个插件可以帮我们优化打包日志，我们打包时候经常看到很长一个日志信息，有的时候是不需要的，也不会去看所以可以用这个插件来简化

作者：杰杰的风
链接：https://juejin.im/post/6888848660591968264
来源：掘金




