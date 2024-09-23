---
title: 为什么需要Webpack
date: 2021-12-21 10:13:24
toc: true
---

想要理解为什么要使用 webpack，我们先回顾下历史，在打包工具出现之前，我们是如何在 web 中使用 JavaScript 的。在浏览器中运行 JavaScript 有两种方法：

**第一种方式**，引用一些脚本来存放每个功能，比如下面这个文档：

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <!-- HTML 代码 -->
    <div>我的HTML代码</div>

    <!-- 引入外部的 JavaScript 文件 -->
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/lodash.js/4.17.21/lodash.core.min.js"></script>

    <!-- 引入我自己的 JavaScript 文件 -->
    <script src="./scripts/common.js"></script>
    <script src="./scripts/user.js"></script>
  </body>
</html>
```

此解决方案很难扩展，因为加载太多脚本会导致网络瓶颈。同时如果你不小心更改了 JavaScript 文件的加载顺序，这个项目可能要崩溃。

**第二种方式**，使用一个包含所有项目代码的大型`.js`文件，对上面的文档做改进:

```html
<!-- index2.html -->
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <!-- HTML 代码 -->
    <div>我的HTML代码</div>
    <!-- 引入我自己的 JavaScript 文件 -->
    <script src="./scripts/bundle.33520ba89e.js"></script>
  </body>
</html>
```

这种方式解决了方式一的问题，但会导致作用域、文件大小
可读性和可维护性方面的问题。如何解决这些问题，请往下阅读。

### 如何解决作用域问题

早先前，我们使用 Grunt 和 Gulp 两个工具来管理我们项目的资源。
这两个工具称为任务执行器，它们将所有项目文件拼接在一起。利用了 立即调用函数 表达式(IIFE) - Immediately invoked function expressions , 解决了大型项目的作用域问题；当脚本文件被封装在 IIFE 内部时，你可以安全地拼接或安全地组合所有文件，而不必担心作用域冲突.

**什么是 IIFE,参见如下的代码：**

- 当函数变成立即执行的函数表达式时，表达式中的变量不能从外部访问

```js
(function () {
  var name = "Barry";
})();
// 无法从外部访问变量 name
// 抛出错误："Uncaught ReferenceError: name is not defined"
```

- 将 IIFE 分配给一个变量，不是存储 IIFE 本身，而是存储 IIFE 执行后返回的结果。

```js
var result = (function () {
  var name = "Barry";
  return name;
})();
// IIFE 执行后返回的结果： result; // "Barry"
```

Grunt ， Gulp 解决了作用域问题。但是，修改一个文件意味着必须重新构建整个文件。拼接可以做到很容易地跨文件重用脚本，却使构建结果的优化变得更加困难。如何判断代码是否实际被使用？

即使你只用到 lodash 中的某个函数，也必须在构建结果中加入整个库，然后将它们压缩在一起。大规模地实现延迟加载代码块及无用代码的去除，需要开发人员手动地进行大量工作。

```html
<!-- index3.html -->
<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdn.bootcdn.net/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script>
  </head>
  <body>
    <script>
      // 这里我们只使用了一个join函数，确要引入整个lodash库 
      const str = _.join(['s1mple', 'top1'], '-')
      console.log(str);
    </script>
  </body>
</html>
```


### 如何解决代码拆分问题
感谢 Node.js ，JavaScript 模块诞生了！
Node.js 是一个 JavaScript 运行时，可以在浏览器环境之外的计算机和服务器中使用。webpack 运行在 Node.js 中。

当 Node.js 发布时，一个新的时代开始了，它带来了新的挑战。既然不是在浏览器中运行 JavaScript，现在已经没有了可以添加到浏览器中的 html 文件和 script 标签。那么 Node.js 应用程序要如何加载新的代码文件呢？

CommonJS 问世并引入了 require 机制，它允许你在当前文件中加载和使用某个模块。导入需要的每个模块，这一开箱即用的功能，帮助我们解决了代码拆分的问题。

Node.js 已经成为一种语言、一个平台和一种快速开发和创建快速应用程序的方式，接管了整个 JavaScript 世界。

但 CommonJS 没有浏览器支持。没有 live binding(实时绑定)。循环引用存在问题。同步执行的模块解析加载器速度很慢。虽然 CommonJS 是 Node.js 项目的绝佳解决方案，但浏览器不支持模块，我们似乎又遇到了新问题。

### 如何让浏览器支持模块
在早期，我们应用 Browserify 和 RequireJS 等打包工具编写能够在浏览器中运行的 CommonJS 模块:

目前，我们还有一个选择，就是来自 Web 项目的好消息是，模块正在成为ECMAScript 标准的官方功能。然而，浏览器支持不完整，版本迭代速度也不够快，还是推荐上面两个早期模块实现。早期的任务构建工具基于 Google 的 Closure 编译器，要求我们手动在顶部声明所有的依赖，开发体验不好。

### Webpack 搞定这一切
是否可以有一种方式，不仅可以让我们编写模块，而且还支持任何模块格式（至少在我们到达 ESM 之前），并且可以同时处理 resource 和 assets ？

这就是 webpack 存在的原因。它是一个工具，可以打包你的 JavaScript 应用程序（支持 ESM 和 CommonJS），可以扩展为支持许多不同的静态资源，例如：images , fonts 和 stylesheets 。

webpack 关心性能和加载时间；它始终在改进或添加新功能，例如：异步地加载和预先加载代码文件，以便为你的项目和用户提供最佳体验

![webpack](/assets/engineeringImg/webpackImg/webpack.png "webpack")

### Webapck与竞品
- Webpack
Webpack为处理资源管理和分割代码而生，可以包含任何类型的文件。灵活，插件多。

- Parcel
Parcel是0配置工具，用户一般无需再做其它配置即可开箱即用

- Rollup
Rollup用标准化的格式(es6)来写代码，通过减少死代码尽可能的缩小包体积。一般只用来打包JS.

**小结论**
构建一个简单的应用并让它快速运行起来？使用Parcel。
构建一个类库只需要导入很少第三方库？使用Rollup。
构建一个复杂的应用，需要集成很多第三方库？需要代码拆分，使用静态资源文件，还有CommonJs依赖? 使用Webpack。
Vite 这种基于 ESmodule 的构建方式会日益受到用户青睐，不仅因为 Vite 按需编译，热模块替换等特性，还有其丝滑的开发体验以及和 Vue 3 的完美结合。
