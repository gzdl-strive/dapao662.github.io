---
title: 渐进式网络应用程序PWA
date: 2021-12-24 16:40:24
toc: true
---

渐进式网络应用程序(`progressive web application - PWA`)，是一种可以提供类似于 native app(原生应用程序) 体验的 web app(网络应用程序)。PWA 可以用来做很多事。其中最重要的是，在离线(`offline`)时应用程序能够继续运行功能。这是通过使用名为 `Service Workers` 的 web 技术来实现的。

## 非离线环境下运行

到目前为止，我们一直是直接查看本地文件系统的输出结果。通常情况下，真正的用户是通过网络访问 web app；用户的浏览器会与一个提供所需资源（例如， .html , .js 和 .css 文件）的 server 通讯。

我们通过搭建一个拥有更多基础特性的 server 来测试下这种离线体验。这里使用`http-server` package： `npm install http-server --save-dev` 。还要修改
package.json 的 scripts 部分，来添加一个 start script：

```json
{
  // ...
  "scripts": {
    "start": "http-server dist"
  }
}
```

注意：默认情况下，`webpack DevServer` 会写入到内存。我们需要启用`devserverdevmiddleware.writeToDisk` 配置项，来让 http-server 处理 ./dist 目录中的文件

```js
module.exports = {
  //...
  devServer: {
    devMiddleware: {
      index: true,
      writeToDisk: true,
    },
  },
};
```

如果你之前没有操作过，先得运行命令 npm run build 来构建你的项目。然后运行命令 npm start 。

如果你打开浏览器访问 http://localhost:8080 (即 http://127.0.0.1 )，你应该会看到 webpack 应用程序被 serve 到 dist 目录。如果停止 server 然后刷新，则 webpack 应用程序不再可访问。这就是我们为实现离线体验所需要的改变。在本章结束时，我们应该要实现的是，停止 server 然后刷新，仍然可以看到应用程序正常运行

## 添加 Workbox

添加 `workbox-webpack-plugin` 插件，然后调整 `webpack.config.js` 文件：

```bash
npm install workbox-webpack-plugin --save-dev
```

**webpack.config.js**

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const WorkboxPlugin = require("workbox-webpack-plugin");

module.exports = {
  entry: {
    app: "./src/index.js",
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new WorkboxPlugin.GenerateSW({
      // 这些选项帮助快速启用 ServiceWorkers
      // 不允许遗留任何“旧的” ServiceWorkers
      clientsClaim: true,
      skipWaiting: true,
    }),
  ],
  output: {
    filename: "[name].bundle.js",
    path: path.resolve(__dirname, "dist"),
    clean: true,
  },
};
```

执行 npx webpack

现在你可以看到，生成了两个额外的文件： `service-worker.js` 和名称冗长的`workbox-718aa5be.js` 。

service-worker.js 是 Service Worker 文件， workbox- 718aa5be.js 是 service-worker.js 引用的文件，所以它也可以运行。你本地生成的文件可能会有所不同；但是应该会有一个 service-worker.js 文件。

所以，值得高兴的是，我们现在已经创建出一个 Service Worker。接下来该做什么？

## 注册 Service Worker

接下来我们注册 Service Worker，使其出场并开始表演。通过添加以下注册代码来完成此操作：

**index.js**

```js
//...
if ("serviceWorker" in navigator) {
  window.addEventListener("load", () => {
    navigator.serviceWorker
      .register("/service- worker.js")
      .then((registration) => {
        console.log("SW registered: ", registration);
      })
      .catch((registrationError) => {
        console.log("SW registration failed: ", registrationError);
      });
  });
}
```
再次运行 npx webpack 来构建包含注册代码版本的应用程序。然后用 npm start启动服务。访问 http://localhost:8080 并查看 console 控制台。在那里你应该看到：
```bash
sw registered
```

现在来进行测试。停止 server 并刷新页面。如果浏览器能够支持 Service Worker，应该可以看到你的应用程序还在正常运行。然而，server 已经停止 serve 整个 dist文件夹，此刻是 Service Worker 在进行 serve
