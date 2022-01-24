---
title: 04_搭建开发环境
date: 2021-12-21 15:48:24
toc: true
---

截止目前，我们只能通过复制`dist/index.html`完整物理路径到浏览器地址栏里访问页面。现在来看看如何设置一个开发环境，是我们的开发体验变得更轻松一些。

### mode选项
在开始前，我们先将mode设置为development
```js
//webpack.config.js
module.exports = {
  //开发模式
  mode: 'development',
}
```

### 使用source-map
当webpack打包源代码时，可能会很难追踪到error(错误)和warning(警告)在源代码中的原始位置。例如，如何将三个源文件(a.js,b.js和c.js)打包到一个bundle(bundle.js)中，而其中一个源文件包含一个错误，那么堆栈跟踪就会直接指向到 bundle.js 。你可能需要准确地知道错误来自于哪个源文件，所以这种提示这通常不会提供太多帮助。

为了更容易地追踪 error 和 warning，Webpack 提供了 source maps 功能，可以将编译后的代码映射回原始源代码。如果一个错误来自于 b.js ，source map 就会明确的告诉你。

在本篇中，我们将使用 `inline-source-map` 选项：
```js
//webpack.config.js
module.exports = {
  //在开发模式下追踪代码
  devtool: 'inline-source-map'
}
```

现在，让我们来做一些调试，在 src/hello-world.js 文件中生成一个错误：
```js
function helloWorld() { 
  // console 单词拼写错误 
  cosnole.log('Hello world') 
}
// 导出函数模块 
export default helloWorld
```
再次编译，现在，在浏览器中打开生成的 index.html 文件，并且在控制台查看显示的错误。

我们可以精确定位错误的行数。

### 使用watch mode(观察模式)
在每次编译代码时，手动运行 npx webpack 会显得很麻烦。
我们可以在 webpack 启动时添加 "watch" 参数。如果其中一个文件被更新，代码将被重新编译，所以你不必再去手动运行整个构建。

执行`npx webpack --watch`

现在命令行中，光标停留在尾部，监测文件的变化。修改 hello-world.js 文件

现在，保存文件并检查 terminal(终端) 窗口。应该可以看到 webpack 自动地重新编译修改后的模块！
唯一的缺点是，为了看到修改后的实际效果，你需要刷新浏览器。如果能够自动刷新浏览器就更好了，因此接下来我们会尝试通过 webpack-dev-server 实现此功能

### 使用webpack-dev-server
webpack-dev-server 为你提供了一个基本的 web server，并且具有 live reloading(实时重新加载) 功能。先安装：
```powershell
npm install --save-dev webpack-dev-server
```
修改配置文件，告知 dev server，从什么位置查找文件：

```js
//webpack.config.js
module.exports = {
  //dev-server
  devServer: {
    static: './dist',
  }
}
```
以上配置告知 webpack-dev-server ，将 dist 目录下的文件作为 web 服务的根目录。

>**提示**：
>webpack-dev-server 在编译之后不会写入到任何输出文件。而是将 bundle 文件保留在内存中，然后将它们 serve 到 server 中，就好像它们是挂载在 server根路径上的真实文件一样

执行命令：`npx webpack serve --open`

在浏览器里可以直接访问页面,如果这时我们修改了hello-world.js文件，我们不用刷新浏览器页面，会自动更新。