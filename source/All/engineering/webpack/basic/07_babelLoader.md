---
title: 使用babel-loader
date: 2021-12-22 10:00:02
toc: true
---

前面的章节里，我们应用 `less-loader` 编译过 less 文件，应用 `xml-loader` 编译过 xml 文件，那 js 文件需要编译吗？我们来做一个实验，修改 hello-world.js 文件：

```js
//hello-world.js
function getString() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("Hello world!!!");
    }, 2000);
  });
}

async function helloWorld() {
  let string = await getString();
  console.log(string);
}

export default helloWorld;
```

执行编译:`npx webpack`

查看 bundle.js 文件

```js
//...
/***/
"./src/hello-world.js": /*!****************************!*\
!*** ./src/hello-world.js ***! \****************************/ /***/
((__unused_webpack_module, __webpack_exports__, __webpack_require__) =>
{
  "use strict";
  __webpack_require__.r(__webpack_exports__);
  /* harmony export */ __webpack_require__.d(__webpack_exports__, {
    /* harmony export */ "default": () => (__WEBPACK_DEFAULT_EXPORT__)
  /* harmony export */ });
  function getString() {
    return new Promise((resolve, reject) => {   
      setTimeout(() => { 
        resolve('Hello world~~~') 
      }, 2000) 
    }) 
  }
  async function helloWorld() {
  let string = await getString() console.log(string) }
  // 导出函数模块
  /* harmony default export */
  const __WEBPACK_DEFAULT_EXPORT__ = (helloWorld); /***/ })
//...
```

可以发现，编写的ES6代码原样输出了。

Hello world~~ 两秒后正常输出，说明浏览器能够运行我们的ES6代码。但如果浏览器版本过低，就很难保证代码正常运行了。

### 为社么需要babel-loader
webpack 自身可以自动加载JS文件，就像加载JSON文件一样，无需任何 loader。可是，加载的JS文件会原样输出，即使你的JS文件里包含ES6+的代码，也不会做任何的转化。这时我们就需要Babel来帮忙。B**abel 是一个 JavaScript 编译器，可以将ES6+转化成ES5。**在Webpack里使用Babel，需要使用 babel-loader 。

### 使用babel-loader
安装：
```powershell
npm install -D babel-loader @babel/core @babel/preset-env
```

>babel-loader: 在webpack里面引用babel解析ES6的桥梁
>@babel/core: babel核心模块
>@babel/preset-env: babel预设，一组babel插件的集合

在 webpack 配置中，需要将 babel-loader 添加到 module 列表中，就像下面这样：
```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  }
}
```
执行编译,查看bundle.js可以看到，async/await的ES6语法被babel编译了.

### gegeneratorRuntime插件
此时执行编译，在浏览器里面打开项目发现报了一个错误:

![babel](/assets/engineeringImg/webpackImg/babel.png "babel")

`regeneratorRuntime`是webpack打包生成的全局辅助函数，由babel生成，用于兼容async/await的语法。

`regeneratorRuntime is not defined` 这个错误显然是未能正确配置babel。

正确的做法需要添加以下的插件和配置：
```powershell
# 这个包中包含了regeneratorRuntime,运行时需要
npm install --save @babel/runtime

# 这个插件会在需要regeneratorRuntime的地方自动require导入包，编译时需要
npm install --save-dev @babel/plugin-transform-runtime

# 更多参考这里 
https://babeljs.io/docs/en/babel-plugin-transform-runtime
```

接着修改以下babel的配置：
```js
module.exports = {
  //...
  rules: [
    {
      test: /\.js$/,
      exclude: /node_modules/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env'],
          plugins: [
            [
              '@babel/plugin-transform-runtime'
            ]
          ]
        }
      }
    }
  ]
}
```
成功运行