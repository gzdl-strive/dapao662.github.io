---
title: 拆分开发环境和生产环境配置
date: 2021-12-22 11:26:02
toc: true
---

现在，我们只能手工的来调整 mode 选项，实现生产环境和开发环境的切换，且很多配置在生产环境和开发环境中存在不一致的情况，比如开发环境没有必要设置缓存，生产环境还需要设置公共路径等等。
本节介绍拆分开发环境和生产环境，让打包更灵活。

### 公共路径

`publicPath`配置选项在各种场景中都非常有用。你可以通过它来指定应用程序中所有资源的基础路径。

- 基于环境设置
  在开发环境中，我们通常有一个 assets/ 文件夹，它与索引页面位于同一级别。这没太大问题，但是，如果我们将所有静态资源托管至 CDN，然后想在生产环境中使用呢？

想要解决这个问题，可以直接使用一个 `environment variable(环境变量)`。假设我们有一个变量 `ASSET_PATH` ：

```js
import webpack from "webpack";
//尝试使用环境变量，否则使用根路径
const ASSET_PATH = process.env.ASSET_PATH || "/";

export default {
  output: {
    publickPath: ASSET_PATH,
  },

  plugins: [
    //这可以帮助我们在代码中安全地使用环境变量
    new webpack.DefinePlugin({
      "process.env.ASSET_PATH": JSON.stringify(ASSET_PATH),
    }),
  ],
};
```

- Automatic publicPath
  有可能你事先不知道 publicPath 是什么，webpack 会自动根据`import.meta.url 、 document.currentScript 、 script.src 或者self.location 变量设置 publicPath`。你需要做的是将 output.publicPath 设为 'auto' ：

```js
module.exports = {
  output: {
    publicPath: "auto",
  },
};
```

请注意在某些情况下不支持 document.currentScript ，例如：IE 浏览器，你不得不引入一个 polyfill，例如 currentScript Polyfill 。

### 环境变量

想要消除 webpack.config.js 在 开发环境 和 生产环境 之间的差异，你可能需要环境变量(environment variable)。
webpack 命令行 环境配置 的 `--env` 参数，可以允许你传入任意数量的环境变量。而在 `webpack.config.js `中可以访问到这些环境变量。例如， `--env production` 或 `--env goal=local` 。

```powershell
npx webpack --env goal=local --env production --progress
```

对于我们的 webpack 配置，有一个必须要修改之处。通常， module.exports 指向配置对象。要使用 env 变量，你必须将 module.exports 转换成一个函数：

```js
//...
module.exports = (env) => {
  return {
    //...之前的配置对象
    //根据命令行参数 env 来设置不同环境的 mode
    mode: env.production ? "production" : "development",
  };
};
```

### 拆分配置文件

目前，生产环境和开发环境使用的是一个配置文件，我们需要将这两个文件单独放到不同的配置文件中。如 `webpack.config.dev.js` （开发环境配置）和`webpack.config.prod.js` （生产环境配置）。在项目根目录下创建一个配置文件夹 `config` 来存放他们。

### npm 脚本

每次打包或启动服务时，都需要在命令行里输入一长串的命令。我们将父目录的 package.json 、 node_modules 与 package-lock.json 拷贝到当前目录下

配置 npm 脚本来简化命令行的输入，这时可以省略 npx ：

```json
[
  {
    "scripts": {
      "start": "webpack serve -c ./config/webpack.config.dev.js",
      "build": "webpack -c ./config/webpack.config.prod.js"
    }
  }
]
```

### 提取公共配置
这时，我们发现这两个配置文件里存在大量的重复代码，可以手动的将这些重复的代码单独提取到一个文件里

创建`webpack.config.common.js` ，配置公共的内容

改写`webpack.config.dev.js`和`webpack.config.prod.js`

### 合并配置文件
配置文件拆分好后，新的问题来了，如何保证配置合并没用问题呢？`webpack-merge` 这个工具可以完美解决这个问题

安装 webpack-merge :
```powershell
npm install webpack-merge -D
```
创建 webpack.config.js ，合并代码:
```js
//webpack.config.js
const { merge } = require('webpack-merge');
const commonConfig = require('./webpack.config.common.js');
const productionConfig = require('./webpack.config.prod.js');
const developmentConfig = require('./webpack.config.dev.js');

module.exports = (env) => {
  switch(true) {
    case env.development: 
      return merge(commonConfig, development);
    case env.production:
      return merge(commonConfig, productionConfig);
    default: 
      throw new Error('NO matching configuration was found!')
  }
}
```


