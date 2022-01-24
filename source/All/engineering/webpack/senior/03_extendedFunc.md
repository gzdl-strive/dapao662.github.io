---
title: 扩展功能
date: 2021-12-24 15:18:24
toc: true
---

## PostCSS 与 CSS 模块

PostCSS 是一个用 JavaScript 工具和插件转换 CSS 代码的工具。比如可以使用 `Autoprefixer` 插件自动获取浏览器的流行度和能够支持的属性，并根据这些数据帮我们自动的 为 CSS 规则添加前缀，将最新的 CSS 语法转换成大多数浏览器都能理解的语法

CSS 模块 能让你永远不用担心命名太大众化而造成冲突，只要用最有意义的名字就行了。

### PostCSS

PostCss 与 Webpack 结合，需要安装`style-loader`,`css-loader`，`postcss-loader`三个 loader:

```js
module.exports = {
  rules: [
    {
      test: /\.css$/,
      exclude: /node_modules/,
      use: [
        {
          loader: "style-loader",
        },
        {
          loader: "css-loader",
          options: {
            importLoaders: 1,
          },
        },
        {
          loader: "postcss-loader",
        },
      ],
    },
  ],
};
```

安装 autoprefixer 插件,和 postcss-nexted

```bash
npm install --save-dev autoprefixer
```

然后在根目录下创建`postcss.config.js`:

```js
//postcss.config.js
module.exports = {
  plugins: [require("autoprefixer"), require("postcss-nested")],
};
```

插件 `autoprefixer` 提供自动给样式加前缀去兼容浏览器， postcss-nested 提供编写嵌套的样式语法

最后，在 package.json 内增加如下实例内容：

```json
{
  "browserslist": ["> 1%", "last 2 versions"]
}
```

数组内的值对应的含义：

1. `last 2 versions` : 每个浏览器中最新的两个版本。
2. `> 1% or >= 1%` : 全球浏览器使用率大于 1%或大于等于 1%

### CSS 模块

目前还有一个问题，就是多人编写的样式可能会冲突，开启 CSS 模块可以解决这个问题。 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          "style-loader",
          {
            loader: "css-loader",
            options: {
              //开启css模块
              modules: true,
            },
          },
          "postcss-loader",
        ],
      },
    ],
  },
};
```

假如项目里有样式文件 `style.css`

```css
body {
  display: flex;
  flex-direction: column;
  .box {
    width: 100px;
    height: 100px;
    background: red;
  }
}
```

在 js 文件里导入 css 文件：

```js
// 开启 css 模块后，可以导入模块
import style from "./style.css";
const div = document.createElement("div");
div.textContent = "hello webpack";
// style 里可以识别 class 样式
div.classList.add(style.box);
document.body.appendChild(div);
```

也可以部分开启 CSS 模块模式，比如全局样式可以冠以 `.global` 前缀，如:

- `*.global.css`普通模式
- `*.css` css module 模式

这里统一用 global 关键词进行识别。用正则表达式匹配文件：

```js
//css module
module.exports = {
  //...
  module: {
    rules: [
      {
        test: new RegExp(`$(?!.*\\.global).*\\.css`),
        use: [
          "style-loader",
          {
            loader: "css-loader",
            options: {
              modules: true,
              localIdentName: "[hash:base64:6]",
            },
          },
          "postcss-loader",
        ],
        exclude: [path.resolve(__dirname, "..", "node_modules")],
      },
    ],
  },
};
```

## Web Works

有时我们需要在客户端进行大量的运算，但又不想让它阻塞我们的 js 主线程。你可能第一时间考虑到的是异步。

但事实上，运算量过大(执行时间过长)的异步也会阻塞 js 事件循环，甚至会导致浏览器假死状态。这时候，HTML5 的新特性 WebWorker 就派上了用场。在此之前，我们简单的了解下这个特性。

html5 之前，打开一个常规的网页，浏览器会启用几个线程？
一般而言，至少存在三个线程(公用线程不计入在内):
分别是 js 引擎线程(处理 js)、GUI 渲染线程(渲染页面)、浏览器事件触发线程(控制交互)。

当一段 JS 脚本长时间占用着处理机,就会挂起浏览器的 GUI 更新，而后面的事件响应也被排在队列中得不到处理，从而造成了浏览器被锁定进入假死状态。

现在如果遇到了这种情况，我们可以做的不仅仅是优化代码————html5 提供了解决方案，webworker。

webWorkers 提供了 js 的后台处理线程的 API，它允许将复杂耗时的单纯 js 逻辑处理放在浏览器后台线程中进行处理，让 js 线程不阻塞 UI 线程的渲染。多个线程间也是可以通过相同的方法进行数据传递。它的使用方式如下:

```js
//new Worker(scriptURL: string | URL, options?: WorkerOptions)
new Worker("someWorker.js");
```

也就是说，需要单独写一个 js 脚本，然后使用 new Worker 来创建一个 Work 线程实例。
这意味着并不是将这个脚本当做一个模块引入进来，而是单独开一个线程去执行这个脚本。

我们知道，常规模式下，我们的 webpack 工程化环境只会打包出一个 bundle.js，那我们的 worker 脚本怎么办？
也许你会想到设置多入口(Entry)多出口(ouotput)的方式。
事实上不需要那么麻烦，webpack4 的时候就提供了 worker-loader 专门配置 webWorker。
令人开心的是，webpack5 之后就不需要用 loader 啦，因为 webpack5 内置了这个功能。
我们来试验一下：

- 第一步
  创建一个 work 脚本 work.js，我们甚至不需要写任何内容，我们的重点不是 webWorker 的使用，而是在 webpack 环境中使用这个特性。当然，也可以写点什么，比如：

```js
self.onmessage = ({ data: { question } }) => {
  self.postMessage({
    answer: 42,
  });
};
```

- 在`index.js`中使用它

```js
// 下面的代码属于业务逻辑
const worker = new Worker(new URL("./work.js", import.meta.url));
worker.postMessage({
  question: "hi，那边的workder线程，请告诉我今天的幸运数字是多少？",
});
worker.onmessage = ({ data: { answer } }) => {
  console.log(answer);
};
```

(`import.meta.url`这个参数能够锁定我们当前的这个模块——注意，它不能在 commonjs 中使用。)
这时候我们执行打包命令，会发现,dist 目录下除了 bundle.js 之外，还有另外一个 xxx.bundle.js!
这说明我们的 webpack5 自动的将被 new Work 使用的脚本单独打出了一个 bundle。
我们加上刚才的问答代码，执行 npm run dev，发现它是能够正常工作。
并且在 network 里也可以发现多了一个 src_worker_js.bundle.js。

**总结：**
webpack5 以来内置了很多功能，让我们不需要过多的配置，比如之前讲过的 hot 模式，和现在的 web workder

## TypeScript

在前端生态里，TS 扮演着越来越重要的角色.
我们直入正题，讲下如何在 webpack 工程化环境中集成 TS
首先，当然是安装我们的 ts 和对应的 loader.

```bash
npm install --save-dev typescript ts-loader
```

接下来我们需要在项目根目录下添加一个 ts 的配置文件————`tsconfig.json`，我们可以用 ts 自带的工具来自动化生成它。

```bash
npx tsc --init
```

我们发现生成了一个 tsconfig.json，里面注释掉了绝大多数配置。现在，根据我们想要的效果来打开对应的配置。

```json
//tsconfig.json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "noImplicitAny": true,
    "sourceMap": true,
    "module": "es6",
    "target": "es5",
    "jsx": "react",
    "allowJs": true,
    "moduleResolution": "node"
  }
}
```

好了，接下来我们新增一个`src/index.ts`，内置一些内容。
然后我们别忘了更改我们的 entry 及配置对应的 loder。
当然，还有`resolve.extensions`，将.ts 放在.js 之前，这样它会先找.ts。
注意，如果我们使用了 sourceMap，一定记得和上面的 ts 配置一样，设置 sourcemap 为 true.

也别忘记在我们的 webpack.config.js 里，添加 sourcemap:
更改如下：

```js
//webpack.config.js
const path = require("path");

module.exports = {
  mode: "development",
  entry: "./src/index.ts",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  devtool: "inline-source-map",
  module: {
    rules: [
      {
        test: /\.(ts|tsx)$/,
        use: "ts-loader",
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: [".tsx", ".ts", ".js"],
  },
};
```

运行我们的项目，我们发现完全没有问题

### 使用第三方类库

在从 npm 上安装第三方库时，一定要记得同时安装这个库的类型声明文件(typing definition)。
我们可以从 TypeSearch 中找到并安装这些第三方库的类型声明文件(`https://www.typescriptlang.org/dt/search?search=`)

- lodash

举个例子，如果想安装 lodash 类型声明文件，我们可以运行下面的命令：

```bash
npm install --save-dev lodash @types/lodash
```

- eslint&ts
  注意，如果要使用 eslint,使用初始化命令的时候，记得选择使用 typescript

```bash
npx eslint --init
# 往下选择的时候选择使用了typescript
```

如果已经配置了 eslint，但没有配置 ts 相关的配置，那么我们需要先安装对应的 plugin

```bash
yarn add -D @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest
```

注意如果需要用到 react 的话，记得也要安装

```bash
yarn add -D eslint-plugin-react@latest
```

vue 或者其他常用框架同样如此，一般都会有专门的 plugin。
然后我们队`.esilntrc`进行更改~

```json
{
  "env": { "browser": true, "es2021": true },
  "extends": [
    "eslint:recommended", // 如果需要react的话
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": { "jsx": true }, // 如果需要react的话
    "ecmaVersion": 13,
    "sourceType": "module"
  },
  "plugins": ["react", "@typescript-eslint"],
  "rules": {
    // ...一些自定义的rules
    "no-console": "error"
  }
}
```
执行npm run eslint,大功告成