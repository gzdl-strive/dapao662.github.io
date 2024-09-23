---
title: 03_自动引入资源
date: 2021-12-21 15:20:24
toc: true
---

到目前为止，我们都是在 index.html 文件中手动引入所有资源，然而随着应用程序增长，如果继续手动管理 index.html 文件，就会变得困难起来。然而，通过一些插件可以使这个过程更容易管控。

### 什么是插件

插件是 webpack 的核心功能。插件可以用于执行一些特定的任务，包括：打包优化，资源管理，注入环境变量等。Webpack 自身也是构建于你在 webpack 配置中用到的**相同的插件系统**之上!

想要使用一个插件，你只需要 `require()` 它，然后把它添加到 plugins 数组中。多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建一个插件实例。

### 使用 HtmlWebpackPlugin

首先安装插件:

```powershell
npm install --save-dev html-webpack-plugin
```

并且调整`webpack.config.js`文件：

```js
module.exports = {
  //...
  plugins: [
    //实例化html-webpack-plugin 插件
    new HtmlWebpackPlugin(),
  ],
};
```

打包,执行 npx webpack
![dist](/assets/engineeringImg/webpackImg/dist_htmlWebpackPlugin.png "dist")
打包生成的`index.html`文件内容如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Webpack App</title>
    <meta name="viewport" content="width=device-width, initial- scale=1" />
    <script defer src="bundle.js"></script>
  </head>
  <body></body>
</html>
```
打包后，我们发现这个`dist/index.html`似乎与先去的`index.html`并没有关系，`HtmlWebpackPlugin`会默认生成它自己的`index.html`文件,并且所偶的bundle(bundle.js)会自动添加到Html中.

能否基于原有的`index.html`文件打包生成新的`index.html`呢? 可以通过阅读`HtmlWebpackPlugin`插件提供的全部功能和选项来查找答案.

首先删除 `index.html` 手工引入的 js 文件.
再次调整 `webpack.config.js` 文件.
```js
//webpack.config.js
module.exports = {
  //...
  plugins: [
    //实例化html-webpack-plugin插件
    new HtmlWebpackPlugin({
      template: './index.html',//打包生成的文件的模板
      filename: 'app.html',//打包生成的文件名称。默认为index.html
      inject: 'body',//设置所有资源文件注入模板的位置。可以设置的值true|'head'|'body'|false,默认值为true
    })
  ]
}
```
执行打包命令 => 这次打包应用到了我们的模板文件 index.html , 并且生成了新的文件 app.html , 文件里自动引用的 `bundle.js` 也从 `<header>` 迁移到了 `<body>` 里。

![dist2](/assets/engineeringImg/webpackImg/dist_htmlWebpackPlugin2.png "dist2")

### 清理dist
仔细留意一下，我们发现 `dist/index.html` 仍旧存在，这个文件是上次生成的残留文件，已经没有用了。可见，webpack 将生成文件并放置在 /dist 文件夹中，但是它不会追踪哪些文件是实际在项目中用到的。通常比较推荐的做法是，在每次构建前清理 /dist 文件夹，这样只会生成用到的文件。让我们使用 `output.clean` 配置项实现这个需求。
```js
//webpack.config.js
module.exports = {
  //...
  output: {
    //...
    //打包前清理dist文件夹
    clean: true,
  }
}
```
再次打包，检查 /dist 文件夹。现在只会看到构建后生成的文件，而没有旧文件！