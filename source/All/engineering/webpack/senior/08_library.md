---
title: 创建library
date: 2021-12-25 14:52:02
toc: true
---

除了打包应用程序，webpack 还可以用于打包 JavaScript library

## 创建一个 library

假设我们正在编写一个名为 `webpack-numbers` 的小的 library，可以将数字 1 到 5 转换为文本表示，反之亦然，例如将 2 转换为 'two'。使用 npm 初始化项目，然后安装 webpack ， webpack-cli 和 lodash ：

```bash
npm install webpack webpack-cli lodash -D
```

我们将 lodash 安装为 `devDependencies` 而不是 dependencies ，因为我们不需要将其打包到我们的库中，否则我们的库体积很容易变大。

```json
// src/ref.json
[
  {
    "num": 1,
    "word": "one"
  },
  {
    "num": 2,
    "word": "two"
  },
  {
    "num": 3,
    "word": "three"
  },
  {
    "num": 4,
    "word": "four"
  },
  {
    "num": 5,
    "word": "five"
  }
]
```

```js
//src/indx.js
import _ from "lodash";
import numRef from "./ref.json";
export function numToWord(num) {
  return _.reduce(
    numRef,
    (accum, ref) => {
      return ref.num === num ? ref.word : accum;
    },
    ""
  );
}

export function wordToNum(word) {
  return _.reduce(
    numRef,
    (accum, ref) => {
      return ref.word === word && word.toLowerCase() ? ref.num : accum;
    },
    -1
  );
}
```

## Webpack 配置

我们可以从如下 webpack 基本配置开始：

```js
//webpack.config.js
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "webpack-numbers.js",
  },
};
```

在上面的例子中，我们将通知 webpack 将 src/index.js 打包到 dist/webpack- numbers.js 中。

## 导出 library

到目前为止，一切都应该与打包应用程序一样，这里是不同的部分 - 我们需要通过 `output.library` 配置项暴露从入口导出的内容

```js
//webpack.config.js
const path = require("path");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "webpack-numbers.js",
    library: "webpackNumbers",
  },
};
```

我们暴露了 webpackNumbers ，以便用户可以通过 script 标签使用

```html
<script src="https://example.org/webpack-numbers.js"></script>
<script>
  window.webpackNumbers.wordToNum("Five");
</script>
```

然而它只能通过被 script 标签引用而发挥作用，它不能运行在 CommonJS、AMD、Node.js 等环境中。
作为一个库作者，我们希望它能够兼容不同的环境，也就是说，用户应该能够通过以下方式使用打包后的库：

- **CommonJS module require**

```js
const webpackNumbers = require("webpack-numbers");
//...

webpackNumber.wordToNum("Two");
```

- **AMD module require**

```js
require(["webpackNumbers"], function (webpackNumbers) {
  // ...
  webpackNumbers.wordToNum("Two");
});
```

- **script tag**

```html
<!DOCTYPE html>
<html>
  ...
  <script src="https://example.org/webpack-numbers.js"></script>
  <script>
    // ... // Global variable 
    webpackNumbers.wordToNum('Five'); 
    // Property in the window object window.
    webpackNumbers.wordToNum('Five'); 
    // ...
  </script>
</html>
```
我们更新 output.library 配置项，将其 type 设置为 'umd'

```js
//webpack.config.js
//...
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'webpack-numbers.js',
    library: {
      name: 'webpackNumbers',
      type: 'umd'
    }
  }
}
```
现在 webpack 将打包一个库，其可以与 CommonJS、AMD 以及 script 标签使用

## 外部化 lodash
现在，如果执行 webpack ，你会发现创建了一个体积相当大的文件。如果你查看这个文件，会看到 lodash 也被打包到代码中。在这种场景中，我们更倾向于把lodash 当作 `peerDependency` 。**也就是说，consumer(使用者) 应该已经安装过lodash** 。因此，你就可以放弃控制此外部 library ，而是将控制权让给使用 library的 consumer

这可以使用 externals 配置来完成:
```js
//webpack.config.js
//..
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'webpack-numbers.js',
    library: {
      name: 'webpackNumbers',
      type: 'umd',
    },
    //修补bug
    globalObject: 'globalThis',
  },
  externals: {
    lodash: {
      commonjs: 'lodash',
      commonjs2: 'lodash',
      amd: 'lodash',
      root: '_',
    }
  }
}
```
这意味着你的 library 需要一个名为 lodash 的依赖，这个依赖在 consumer 环境中必须存在且可用。

## 外部化限制

对于想要实现从一个依赖中调用多个文件的那些 library：
```js
import A from 'library/one'; 
import B from 'library/two'; 
// ...
```

无法通过在 externals 中指定整个 library 的方式，将它们从 bundle 中排除。而是需要逐个或者使用一个正则表达式，来排除它们
```js
//webpack.config.js
module.exports = {
  //...
  externals: [
    'library/one',
    'library/two',
    //匹配以"library/"开始的所有依赖
    /^library\/.+$/,
  ]
}
```

## 优化输出

为优化生产环境下的输出结果，我们还需要将生成 bundle 的文件路径，添加到package.json 中的 main 字段中。

```json
{
  //...
  "main": "dist/webpack-numbers.js",
  //...
}
```
或者，按照这个 指南，将其添加为标准模块：
```json
{
  //...
  "module": "src/indexjs",
  //...
}
```
这里的 `key`(键) main 是参照 package.json 标准，而 module 是参照 一个提案，此提案允许 JavaScript 生态系统升级使用 ES2015 模块，而不会破坏向后兼容性

## 发布为 npm package

现在，你可以 将其发布为一个 npm package，并且在 unpkg.com 找到它，并分发给你的用户。

**1、首先进入npmjs.com这个网站**
注册并登录自己的账号，点击自己的头像，进入packages,这里可以查看我们上传过的包

**2、进入我们自己的项目目录**
在控制台输入:`npm config get registry`可以查看到我们的npm源，如果是`https://registry.npmjs.org/`说明我们用的就是Npm官方的源，如果是淘宝的镜像源，需要切换回npm官方源

设置源命令如下

```bash
npm config set registry https://registry.npmjs.org/
npm config set registry https://registry.npm.taobao.org/
```

切换为npmjs.org源成功后

**3、发布**
执行 npm adduser, 输入我们登录npmjs.org网站的用户名，密码，邮箱
登录成功后，执行`npm publish`
发布成功后，我们就可以打开浏览器，进入npmjs.org的packages查看我们发布好包了


