---
title: 缓存
date: 2021-12-22 11:26:02
toc: true
---

以上，我们使用 webpack 来打包我们的模块化后的应用程序，webpack 会生成一个可部署的 /dist 目录，然后把打包后的内容放置在此目录中。只要 /dist 目录中的内容部署到 server 上，client（通常是浏览器）就能够访问此 server 的网站及其资源。而最后一步获取资源是比较耗费时间的，这就是为什么浏览器使用一种名为缓存 的技术。可以通过命中缓存，以降低网络流量，使网站加载速度更快，然而，如果我们在部署新版本时不更改资源的文件名，浏览器可能会认为它没有被更新，就会使用它的缓存版本。由于缓存的存在，当你需要获取新的代码时，就会显得很棘手。
本节通过必要的配置，以确保 webpack 编译生成的文件能够被客户端缓存，而在文件内容变化后，能够请求到新的文件。

### 输出文件的文件名

我们可以通过替换 `output.filename` 中的 `substitutions` 设置，来定义输出文件的名称。webpack 提供了一种使用称为 `substitution`(可替换模板字符串) 的方式，通过带括号字符串来模板化文件名。其中， `[contenthash]` substitution 将根据资源内容创建出唯一 hash。当资源内容发生变化时， `[contenthash]` 也会发生变化。

修改配置文件
```js
//webpack.config.js
module.exports = {
  //...
  output: {
    filename: '[name].[contenthash].js'
  }
}
```
执行打包编译
![cache1](/assets/engineeringImg/webpackImg/cache1.png "文件名")

可以看到，bundle 的名称是它内容（通过 hash）的映射。如果我们不做修改，然后再次运行构建，文件名会保持不变。

### 缓存第三方库

将第三方库(library)（例如 lodash ）提取到单独的 vendor chunk 文件中，是比较推荐的做法，这是因为，它们很少像本地的源代码那样频繁修改。因此通过实现以上步骤，利用 client 的长效缓存机制，命中缓存来消除请求，并减少向 server 获取资源，同时还能保证 client 代码和 server 代码版本一致。 我们在optimization.splitChunks 添加如下 cacheGroups 参数并构建：
```js
module.exports = {
  //...
  splitChunks: {
    cacheGroups: {
      vendor: {
        test: /[\\/]node_modules[\\/]/,
        name: 'verdors',
        chunks: 'all'
      }
    }
  }
}
```
执行编译
![cache2](/assets/engineeringImg/webpackImg/cache2.png "缓存第三方库")

### 将js文件放到一个文件夹中
目前，全部 js 文件都在 dist 文件夹根目录下，我们尝试把它们放到一个文件夹中，这个其实也简单，修改配置文件：
```js
module.exports = {
  //...
  output: {
    filename: 'script/[name].[contenthash].js'
  }
}
```
我们在输出配置中修改 filename ，在前面加上路径即可。执行编译.

截止目前，我们已经把 JS 文件、样式文件及图片等资源文件分别放到了 scripts 、 styles 、 images 三个文件夹中