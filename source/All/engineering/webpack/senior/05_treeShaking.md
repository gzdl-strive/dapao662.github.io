---
title: Tree Shaking
date: 2021-12-24 12:20:24
toc: true
---

tree shaking 是一个术语，通常用于描述移除 JavaScript 上下文中的未引用代码`(dead-code)`。它依赖于 ES2015 模块语法的 静态结构 特性，例如 import 和 export 。这个术语和概念实际上是由 ES2015 模块打包工具 rollup 普及起来的。

webpack 2 正式版本内置支持 ES2015 模块（也叫做 `harmony modules`）和未使用模块检测能力。新的 webpack 4 正式版本扩展了此检测能力，通过 package.json 的 "`sideEffects`" 属性作为标记，向 compiler 提供提示，表明项目中的哪些文件是 "pure(纯正 ES2015 模块)"，由此可以安全地删除文件中未使用的部分

## Tree-shaking 实验

- `src/math.js`

```js
export function square(x) {
  return x * x;
}
export function cube(x) {
  return x * x * x;
}
```

需要将 mode 配置设置成 development，以确定 bundle 不会被压缩：
**webpack.config.js**

```js
const path = require("path");
module.exports = {
  entry: "./src/index.js",
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist"),
  },
  mode: "development",
  optimization: {
    usedExports: true,
  },
};
```

配置完这些后，更新入口脚本，使用其中一个新方法：

```js
import { cube } from "./math.js";
function component() {
  const element = document.createElement("pre");
  element.innerHTML = ["Hello webpack!", "5 cubed is equal to " + cube(5)].join(
    "\n\n"
  );
  return element;
}
document.body.appendChild(component());
```

注意，我们没有从 `src/math.js` 模块中 import 另外一个 square 方法。这个函数就是所谓的“未引用代码(`dead code`)”，也就是说，应该删除掉未被引用的 export 。现在运行 npm script, `npm run build` ，并查看输出的 bundle

注意，上面的 unused harmony export square 注释。如果你观察它下面的代码，你会注意到虽然我们没有引用 square ，但它仍然被包含在 bundle 中

- mode: production
  如果此时修改配置：

```js
const path = require("path");
module.exports = {
  entry: "./src/index.js",
  output: { filename: "bundle.js", path: path.resolve(__dirname, "dist") },
  mode: "production",
};
```
打包后发现无用的代码全部都消失了。
处于好奇，webpack是如何完美的避开没有使用的代码的呢？
很简单：就是 Webpack 没看到你使用的代码。Webpack 跟踪整个应用程序的`import/export` 语句，因此，如果它看到导入的东西最终没有被使用，它会认为那是未引用代码(或叫做“死代码”—— `dead-code` )，并会对其进行 tree-shaking 。
死代码并不总是那么明确的。下面是一些死代码和“活”代码的例子：
```js
// 这会被看作“活”代码，不会做 tree-shaking 
import { add } from './math'
console.log(add(5, 6))

// 导入并赋值给 JavaScript 对象，但在接下来的代码里没有用到 
// 这就会被当做“死”代码，会被 tree-shaking 
import { add, minus } from './math' 
console.log(add(5, 6)) 

// 导入但没有赋值给 JavaScript 对象，也没有在代码里用到 
// 这会被当做“死”代码，会被 tree-shaking 
import { add, minus } from './math' 
console.log('hello webpack') 

// 导入整个库，但是没有赋值给 JavaScript 对象，也没有在代码里用到 
// 非常奇怪，这竟然被当做“活”代码，因为 Webpack 对库的导入和本地代码导入的处理 方式不同。 
import { add, minus } from './math' 
import 'lodash' 
console.log('hello webpack')
```

## sideEffects
注意 Webpack 不能百分百安全地进行 tree-shaking。有些模块导入，只要被引入，就会对应用程序产生重要的影响。一个很好的例子就是全局样式表，或者设置全局配
置的JavaScript 文件。

Webpack 认为这样的文件有“副作用”。具有副作用的文件不应该做 tree-shaking，因为这将破坏整个应用程序。

Webpack 的设计者清楚地认识到不知道哪些文件有副作用的情况下打包代码的风险，因此webpack4默认地将所有代码视为有副作用。这可以保护你免于删除必要的文件，但这意味着 Webpack 的默认行为实际上是不进行 tree-shaking。值得注意的是webpack5默认会进行 tree-shaking。

如何告诉 Webpack 你的代码无副作用，可以通过 package.json 有一个特殊的属性`sideEffects`，就是为此而存在的。

它有三个可能的值：
  - true
  如果不指定其他值的话。这意味着所有的文件都有副作用，也就是没有一个文件可以 tree-shaking。
  - false
  告诉 Webpack 没有文件有副作用，所有文件都可以 tree-shaking。
  - 数组[…]
  是文件路径数组。它告诉 webpack，除了数组中包含的文件外，你的任何文件都没有副作用。因此，除了指定的文件之外，其他文件都可以安全地进行 tree-shaking


webpack4 曾经不进行对 CommonJs 导出和 require() 调用时的导出使用分析。

webpack 5 增加了对一些 CommonJs 构造的支持，允许消除未使用的 CommonJs导出，并从 require() 调用中跟踪引用的导出名称。