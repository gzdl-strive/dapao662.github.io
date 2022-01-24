---
title: 小试Webpack
date: 2021-12-21 11:04:24
toc: true
---

### 安装 Webpack

- 前提条件
  在开始之前，请确保安装了 Node.js 的最新版本。使用 Node.js 最新的长期支持版本(LTS - Long Term Support)，是理想的起步。 使用旧版本，你可能遇到各种问题，因为它们可能缺少 webpack 功能， 或者缺少相关 package。你可以选择下载适合自己平台的安装包，自行安装即可，本文不再赘述。

  > 测试：之前 Node.js 使用的是 12 版本，安装 babel 相关插件会报错，就是因为，版本太低，后来改用 14 版本就可以

- 本地安装

```powershell
npm install --save-dev webpack
# 或指定版本
npm install --save-dev webpack@<version>
```

> **提示**
> 是否使用--save-dev 取决于你的应用场景。假设你仅使用 Webpack 进行构建操作，那么建议你在安装时使用`--save-dev`选项，因为可能你不需要在生产环境上使用 webpack。如果需要应用于生产环境，请忽略--save-dev 选项。

如果你使用 webpack v4+ 版本，并且想要在命令行中调用 webpack ，你还需要安装 CLI。

```powershell
npm install --save-dev webpack-cli
```

对于大多数项目，我们建议本地安装。这可以在引入重大更新(breaking change)版本时，更容易分别升级项目。 通常会通过运行一个或多个 npm scripts 以在本地 node_modules 目录中查找安装的 webpack， 来运行 webpack：

```js
"scripts": {
  "build": "webpack --config webpack.config.js"
}
```

> **提示**
> 想要运行本地安装的 webpack，你可以通过 node_modules/.bin/webpack 来访问它的二进制版本。另外，如果你使用的是 npm v5.2.0 或更高版本，则可以运行 npx webpack 来执行

- 全局安装

```powershell
npm install --global webpack
```

> **提示：**
> 不推荐 全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中， 可能会导致构建失败。

- 最新体验版本
  如果你热衷于使用最新版本的 webpack，你可以使用以下命令安装 beta 版本， 或者直接从 webpack 的仓库中安装：

```powershell
npm install --save-dev webpack@next
# 或特定的 tag/分支
npm install --save-dev webpack/webpack#<tagname/branchname>
```

> **提示：**
> 安装这些最新体验版本时要小心！它们可能仍然包含 bug，因此不应该用于生产环境。

### 运行 Webpack

```js
// src/hello-world.js
function helloWorld() {
  console.log("Hello World");
}
export default helloWorld;

// src/index.js
import helloWorld from "./hello-world.js";

helloWorld();
```

```html
<!-- index.html -->
...
<script src="./src/index.js"></script>
...
```

进入项目目录，运行 Webpack，结果如下

```
[felix] 03-try-webpack $ npx webpack
asset main.js 50 bytes [emitted] [minimized] (name: main) orphan modules 81 bytes [orphan] 1 module ./src/index.js + 1 modules 135 bytes [built] [code generated] WARNING in configuration The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set 'mode' option to 'development' or 'production' to enable defaults for each environment. You can also set it to 'none' to disable any default behavior. Learn more: https://webpack.js.org/configuration/mode/ webpack 5.54.0 compiled with 1 warning in 197 ms
```

在这里，我们在没有任何配置的情况下运行 Webpack（通常你会为 Webpack 提供一个配置文件，现在，自 Webpack4 开始，可以使用默认配置来打包文件了）。这里还有一个警告：“mode” 选项尚未设置。我们将在本课程后面讨论“mode”选项。
从结果来看，webpack 为我们生成了一个 main.js 文件，具体见下图：
![main](/assets/engineeringImg/webpackImg/dist_main.js.png "main")

我们来看一下 main.js 里有什么：

```js
// dist/main.js
(() => {
  "use strict";
  console.log("Hello world");
})();
```

生成的代码非常简洁。这时你可能不禁会问，这个代码是从哪些文件里生成出来的呢？回到终端，我们再运行一下命令：

```
[xxx] $ npx webpack --stats detailed
PublicPath: auto
asset main.js 50 bytes {179} [compared for emit] [minimized] (name: main)
Entrypoint main 50 bytes = main.js
chunk {179} (runtime: main) main.js (main) 180 bytes [entry] [rendered]
> ./src main
orphan modules 103 bytes [orphan] 1 module
./src/index.js + 1 modules [860] 180 bytes {179} [depth 0] [built] [code generated] [no exports] [no exports used]
```

我们看到， asset main.js 是从入口 > ./src main 生成的。那么，我们能自己配置这个入口吗？请看下一节，自定义 Webpack 配置

### 自定义 Webpack 配置

实际上， webpack-cli 给我们提供了丰富的终端命令行指令，可以通过 webpack -- help 来查看

可是命令行不方便也不直观，而且还不利于保存配置的内容。因此，webpack 还给我们提供了通过配置文件，来自定义配置参数的能力。

```js
//webpack.config.js
module.exports = { 
  entry: './src/index.js', 
  output: { 
    filename: 'bundle.js', 
    // 输出文件夹必须定义为绝对路径 
    path: './dist' 
  }, 
  mode: 'none'
}
```
在项目目录下运行 npx webpack , 可以通过配置文件来帮我们打包文件.
```powershell
[felix] 03-try-webpack $ npx webpack 
[webpack-cli] Invalid configuration object. Webpack has been initialized using a configuration object that does not match the API schema. 
  - configuration.output.path: The provided value "./dist" is not an absolute path! 
  -> The output directory as **absolute path** (required).
```

我们发现，打包并没有成功，因为 webpack 要求我们打包配置 output.path 的路径必须为绝对路径，通过 path 模块来定义输出路径为绝对路径：
```js
//webpack.config.js
const path = require('path') 
module.exports = { 
  entry: './src/index.js', 
  output: { 
    filename: 'bundle.js', 
    // 输出文件夹必须定义为绝对路径 
    path: path.resolve(__dirname, './dist') 
  }, 
  mode: 'none' 
}
```
再次打包，打包成功

### 重新运行项目
项目文件通过 webpack 打包好了，可是我们在浏览器运行 index.html 提示如下错误:
<span style="color: red">Uncaugh SyntaxError: Cannot use import statement outside a module</span>
这是因为页面引用的JS代码，在浏览器里不能正确解析了，我们得去引用打包好了的JS才对。修改 index.html
```html
<!-- index.html -->
<!-- <script src="./src/index.js"></script> --> 
<!-- 引用打包好的 JS 文件 --> 
<script src="./dist/bundle.js"></script>
```
在浏览器里再次运行 index.html : 正常运行