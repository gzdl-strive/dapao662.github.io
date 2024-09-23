---
title: 模块联邦
date: 2021-12-25 15:26:24
toc: true
---

## 什么是模块联邦

多个独立的构建可以组成一个应用程序，这些独立的构建之间不应该存在依赖关系，因此可以单独开发和部署它们。

这通常被称作微前端，但并不仅限于此

Webpack5 模块联邦让 Webpack 达到了线上 Runtime 的效果，让代码直接在项目间利用 CDN 直接共享，不再需要本地安装 Npm 包、构建再发布了

我们知道 Webpack 可以通过 DLL 或者 Externals 做代码共享时 Common Chunk，但不同应用和项目间这个任务就变得困难了，我们几乎无法在项目之间做到按需热插拔

**NPM 方式共享模块**
想象一下正常的共享模块方式，对，就是 NPM。
如下图所示，正常的代码共享需要将依赖作为 Lib 安装到项目，进行 Webpack 打包构建再上线，如下图：

对于项目 Home 与 Search，需要共享一个模块时，最常见的办法就是将其抽成通用依赖并分别安装在各自项目中。
虽然 Monorepo 可以一定程度解决重复安装和修改困难的问题，但依然需要走本地编译。

**UMD 方式共享模块**
真正 Runtime 的方式可能是 UMD 方式共享代码模块，即将模块用 Webpack UMD 模式打包，并输出到其他项目中。这是非常普遍的模块共享方式：

对于项目 Home 与 Search，直接利用 UMD 包复用一个模块。但这种技术方案问题也很明显，就是包体积无法达到本地编译时的优化效果，且库之间容易冲突。

**微前端方式共享模块**
微前端：`micro-frontends` (MFE) 也是最近比较火的模块共享管理方式，微前端就是要解决多项目并存问题，多项目并存的最大问题就是模块共享，不能有冲突。

由于微前端还要考虑样式冲突、生命周期管理，所以本文只聚焦在资源加载方式上。
微前端一般有两种打包方式：

1. 子应用独立打包，模块更解耦，但无法抽取公共依赖等。
2. 整体应用一起打包，很好解决上面的问题，但打包速度实在是太慢了，不具备水平扩展能力。

**模块联邦方式**
终于提到本文的主角了，作为 Webpack5 内置核心特性之一的 `Federated Module`

这个方案是直接将一个应用的包应用于另一个应用，同时具备整体应用一起打包的公共依赖抽取能力。

## 应用案例

本案例模拟三个应用： Nav 、 Search 及 Home 。每个应用都是独立的，又通过模块联邦联系到了一起

### 1、Nav 导航
**src/Nav.js**

```js
const NavList = (num) => {  
  let str = '<ul>';
  for(let i = 0; i < num; i++) {
    str += '<li>item(' + i + ')</li>';
  }
  str += '</ul>';
  return str;
}

export default NavList;
```

**src/index.js**
```js
import NavList from './Nav';
console.log('Nav----');

import('header/Header')
  .then(({ default: Header }) => {
    Header();
    const div = document.createElement('div');
    div.style.cssText = 'background: red';
    div.innerHTML = NavList(5);
    document.body.appendChild(div);
  })
```

**webpack.config.js**
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { ModuleFederationPlugin } = require('webpack').container;

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    clean: true
  },
  devServer: {
    static: path.resolve(__dirname, './dist'),
    port: 3002
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new ModuleFederationPlugin({
      name: 'nav',
      filename: 'remoteEntry.js',
      remotes: {
        header: "header@http://localhost:3001/remoteEntry.js",
      },
      exposes: {
        './Nav': './src/Nav.js',
      },
      shared: {},
    })
  ]
}
```
应用 webpack 运行服务：
```bash
npx webpack serve 
```

### 2、Header
**src/Header.js**
```js
const Header = () => {
  const h1 = document.createElement('h1');
  h1.textContent = "Header组件";
  h1.style.cssText="border: 1px solid #f00";
  document.body.appendChild(h1);
}

export default Header;
```

**src/index.js**
```js
import Header from './Header';

Header();

console.log('Header---------');
```

**webpack.config.js**
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { ModuleFederationPlugin } = require('webpack').container;

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    clean: true
  },
  devServer: {
    static: path.resolve(__dirname, './dist'),
    port: 3001
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new ModuleFederationPlugin({
      //模块联邦名字
      name: 'header',
      //外部访问的资源名字
      filename: 'remoteEntry.js',
      //引用的外部资源列表
      remotes: {},
      //暴露给外部资源列表
      exposes: {
        './Header': './src/Header.js',
      },
      //共享模块
      shared: {},
    })
  ]
}
```

### 3、search模块
**src/index.js**
```js
Promise.all([
  import('header/Header').then(({ default: Header }) => {
    Header()
  }),
  import('nav/Nav').then((NavList) => {
    const div = document.createElement('div');
    div.style.cssText = 'background: aqua';
    div.innerHTML = NavList.default(8);
    document.body.appendChild(div);
  })
])

console.log('Search------');
```

**webpack.config.js**
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { ModuleFederationPlugin } = require('webpack').container;

module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    clean: true
  },
  devServer: {
    static: path.resolve(__dirname, './dist'),
    port: 3003
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new ModuleFederationPlugin({
      name: 'search',
      filename: 'remoteEntry.js',
      remotes: {
        header: 'header@http://localhost:3001/remoteEntry.js',
        nav: 'nav@http://localhost:3002/remoteEntry.js'
      },
      exposes: {},
      shared: {},
    })
  ]
}
```

