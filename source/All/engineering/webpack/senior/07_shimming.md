---
title: shimming预置依赖
date: 2021-12-25 13:43:24
toc: true
---

`webpack compiler`能够识别遵循 ES2015 模块语法、CommonJS 或 AMD 规范编写的模块。然而，一些 third party(第三方库)可能会引起一些全局依赖(例如：jQuery 中的$)。因此这些 library 也可能会创建一些需要导出的全局变量。这些"broken modules(不符合规范的模块)"就是 shimming（预置依赖）发挥作用的地方。

shim 另外一个极其有用的使用场景就是：当你希望`polyfill`扩展浏览器能力，来支持到更多用户时。在这种情况下，你可能只是想要将这些 polyfills 提供给需要修补(patch)的浏览器(也就是实现按需加载).

## Shimming 预置全局变量

让我们开始第一个 shimming 全局变量的用例。还记得我们之前用过的 lodash 吗？出于演示目的，例如把这个应用程序中的模块依赖，改为一个全局变量依赖。要实现这些，我们需要使用 `ProvidePlugin` 插件(webpack 自带的)。

使用 ProvidePlugin 后，能够在 webpack 编译的每个模块中，通过访问一个变量来获取一个 package。如果 webpack 看到模块中用到这个变量，它将在最终 bundle 中引入给定的 package。让我们先移除 lodash 的 import 语句，改为通过插件提供它：

**src/index.js**

```js
console.log(_.join(["hello", "webpack"], "-"));
```

**webpack.config.js**

```js
const path = require("path");
const webpack = require("webpack");

module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    filaname: "[name].bundle.js",
    path: path.resolve(__dirname, "dist"),
    clean: true,
  },

  plugins: [
    new webpack.ProvidePlugin({
      _: "lodash",
    }),
  ],
};
```

我们本质上所做的，就是告诉 webpack……

> 如果你遇到了至少一处用到 \_ 变量的模块实例，那请你将 lodash package 引入进来，并将其提供给需要用到它的模块。

还可以使用 `ProvidePlugin` 暴露出某个模块中单个导出，通过配置一个**数组路径**（例如 `[module, child, ...children?]` ）实现此功能。所以，我们假想如下，无论 join 方法在何处调用，我们都只会获取到 lodash 中提供的 join 方法。

```js
//webpack.config.js
const webpack = require("webpack");
module.exports = {
  //...
  plugins: [
    new webpack.ProvidePlugin({
      //_: 'lodash',
      join: ["lodash", "join"],
    }),
  ],
};
```

这样就能很好的与 tree shaking 配合，将 lodash library 中的其余没有用到的导出去除。

## 细粒度 Shimming

一些遗留模块依赖的 this 指向的是 window 对象。在接下来的用例中，调整我们的 index.js:

```js
//index.js
this.alert("hello webpack");
```

当模块运行在 **CommonJS 上下文**中，这将会变成一个问题，也就是说此时的 this 指向的是 `module.exports` 。在这种情况下，你可以通过使用 `imports-loader` 覆 盖 this 指向.
安装 imports-loader

```bash
npm install --save-dev imports-loader
```

修改配置:

```js
//webpack.config.js
const webpack = require("webpack");
//...

module.exports = {
  //...
  module: {
    rules: [
      {
        test: require.resolve("./src/index.js"),
        use: "imports-loader?wrapper=window",
      },
    ],
  },
  //...
};
```

## 全局 Exports

让我们假设，某个 library 创建出一个全局变量，它期望 `consumer`(使用者) 使用这个变量。为此，我们可以在项目配置中，添加一个小模块来演示说明:

首先安装 exports-loader:

```bash
npm install --save-dev exports-loader
```

**src/globals.js**

```js
//假设这是一个第三方的库，但是我们不知道它是使用什么方法导出，而我们也不是如何导入

const file = "这是一个第三方的变量，变量名file";

const helpers = {
  increment: function (x, y) {
    console.log("第三方，新增increment方法");
    return x + y;
  },
  decrement: function (x, y) {
    console.log("第三方，减少decrement方法");
    return x - y;
  },
};
```

**webpack.config.js**

```js
const webpack = require("webpack");
//...

module.exports = {
  //...
  module: {
    rules: [
      {
        test: require.resolve("./src/index.js"),
        use: "imports-loader?wrapper=window",
      },
      {
        test: require.resolve("./src/globals.js"),
        use: "exports-loader?type=commonjs&exports=file,multiple|helpers.increment|increment",
      },
    ],
  },
  //...
};
```

此时，在我们的 entry 入口文件中（即 `src/index.js` ），可以使用 const { file, increment } = require('./globals.js'); ，可以保证一切将顺利运行

## 加载 Polyfills

目前为止，我们讨论的所有内容 都是处理那些遗留的 package，让我们进入到第二个话题：`polyfill`

有很多方法来加载 polyfill。例如，想要引入` @babel/polyfill` 我们只需如下操作：

```bash
npm install --save @bael/polyfill
```

然后，使用 import 将其引入到我们的主 bundle 文件：

```js
import "@babel/polyfill";

console.log(Array.from([1, 2, 3]), (x) => x * x);
```

注意，这种方式优先考虑正确性，而不考虑 bundle 体积大小。为了安全和可靠，`polyfill/shim` 必须运行于所有其他代码之前，而且需要同步加载，或者说，需要在所有 polyfill/shim 加载之后，再去加载所有应用程序代码。 社区中存在许多误解，即现代浏览器“不需要”polyfill，或者 polyfill/shim 仅用于添加缺失功能 - 实际上，它们通常用于修复损坏实现(repair broken implementation)，即使是在最现代的浏览器中，也会出现这种情况。 因此，最佳实践仍然是，不加选择地和同步地加载所有 polyfill/shim，尽管这会导致额外的 bundle 体积成本。

## 进一步优化 Polyfills

不建议使用 `import @babel/polyfilll` 。因为这样做的缺点是会全局引入整个 polyfill 包，比如 Array.from 会全局引入，不但包的体积大，而且还会污染全局环境。

`babel-preset-env` package 通过 `browserslist` 来转译那些你浏览器中不支持的特性。这个 preset 使用 `useBuiltIns` 选项，默认值是 false ，这种方式可以将全局`babel-polyfill` 导入，改进为更细粒度的 import 格式：

```js
import "core-js/modules/es7.string.pad-start";
import "core-js/modules/es7.string.pad-end";
import "core-js/modules/web.timers";
import "core-js/modules/web.immediate";
import "core-js/modules/web.dom.iterable";
```

**安装@babel/preset-env 及相关的包**

```bash
npm i babel-loader @babel/core @babel/preset-env -D
```

**webpack.config.js**

```js
const HtmlWebpackPlugin = require("html-webpack-plugin");
module.exports = {
  mode: "production",
  entry: "./src/index.js",
  plugins: [new HtmlWebpackPlugin()],
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: [
              [
                "@babel/preset-env",
                {
                  targets: ["last 1 version", "> 1%"],
                  useBuiltIns: "usage",
                },
              ],
            ],
          },
        },
      },
    ],
  },
};
```

**useBuiltIns: 参数有 “entry”、”usage”、false 三个值**

默认值是 false ，此参数决定了babel打包时如何处理`@babel/polyfilll` 语句

**“entry”**: 会将文件中 `import @babel/polyfilll` 语句 结合 targets ，转换为一系列引入语句，去掉目标浏览器已支持的 polyfilll 模块，不管代码里有没有用到，只要目标浏览器不支持都会引入对应的 polyfilll 模块。

**“usage”**: 不需要手动在代码里写 i`mport @babel/polyfilll` ，打包时会自动根据实际代码的使用情况，结合 targets 引入代码里实际用到部分 polyfilll 模块

**false**: 对 `import‘@babel/polyfilll’`不作任何处理，也不会自动引入 polyfilll 模块。

需要注意的是在 webpack 打包文件配置的 entry 中引入的 `@babel/polyfill` 不会根据useBuiltIns 配置任何转换处理。

由于@babel/polyfill在7.4.0中被弃用，我们建议直接添加corejs并通过corejs选项设置版本。

此时执行编译，发现报错，报错提示需要我们安装core-js@3包
```bash
npm i core-js@3 -S
```

修改配置
```js
//webpack.config.js
module.exports = {
  //...
  module: {
    rules: [
      {
        //...
        use: {
          loader: 'babel-loader',
          options: {
            preset: [
              [
                '@babel/preset-env',
                {
                  targets: [
                    "last 1 version",
                    "> 1%",
                  ],
                  useBuiltIns: 'usage',
                  //添加corejs配置
                  corejs: 3,
                }
              ]
            ]
          }
        }
      }
    ]
  }
}
```
