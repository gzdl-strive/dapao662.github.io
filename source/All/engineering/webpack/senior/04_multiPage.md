---
title: 多页面应用
date: 2021-12-24 12:20:24
toc: true
---

## entry 配置

### 单个入口(简写)语法

用法：`entry: string | [string]`
**webpack.config.js**

```js
module.exports = {
  entry: "./path/to/my/entry/file.js",
};
```

我们也可以将一个文件路径数组传递给 entry 属性，这将创建一个所谓的 `"multi-main entry"`。在你想要一次注入多个依赖文件，并且将它们的依赖关系绘制在一个"chunk" 中时(合并)，这种方式就很有用。

**webpack.config.js**

```js
module.exports = {
  entry: ["./src/file_1.js", "./src/file_2.js"],
  output: { filename: "bundle.js" },
};
```

当你希望通过一个入口（例如一个库）为应用程序或工具快速设置 webpack 配置时，单一入口的语法方式是不错的选择。然而，使用这种语法方式来扩展或调整配置的灵活性不大

### 对象语法

用法：`entry: { <entryChunkName> string | [string] | {} }`
**webpack.config.js**

```js
module.exports = {
  entry: {
    app: "./src/app.js",
    adminApp: "./src/adminApp.js",
  },
};
```

对象语法会比较繁琐。然而，这是应用程序中定义入口的最可扩展的方式。

**描述入口的对象:**
用于描述入口的对象。你可以使用如下属性：

  - `dependOn` : 当前入口所依赖的入口。它们必须在该入口被加载前被加载。
  - `filename` : 指定要输出的文件名称。
  - `import` : 启动时需加载的模块。
  - `library` : 指定 library 选项，为当前 entry 构建一个 library。
  - `runtime` : 运行时 chunk 的名字。如果设置了，就会创建一个新的运行时 chunk。在 webpack 5.43.0 之后可将其设为 false 以避免一个新的运行时 chunk。
  - `publicPath` : 当该入口的输出文件在浏览器中被引用时，为它们指定一个公共 URL 地址。请查看 output.publicPath。
  **webpack.config.js**

```js
module.exports = {
  entry: {
    a2: "dependingfile.js",
    b2: {
      dependOn: "a2",
      import: "./src/app.js",
    },
  },
};
```

runtime 和 dependOn 不应在同一个入口上同时使用，所以如下配置无效，并且会抛出错误：
**webpack.config.js**

```js
module.exports = {
  entry: {
    a2: "./a",
    b2: {
      runtime: "x2",
      dependOn: "a2",
      import: "./b",
    },
  },
};
```

确保 runtime 不能指向已存在的入口名称，例如下面配置会抛出一个错误：

```js
module.exports = {
  entry: {
    a1: "./a",
    b1: {
      runtime: "a1",
      import: "./b",
    },
  },
};
```

另外 dependOn 不能是循环引用的，下面的例子也会出现错误

```js
module.exports = {
  entry: {
    a3: {
      import: "./a",
      dependOn: "b3",
    },
    b3: {
      import: "./b",
      dependOn: "a3",
    },
  },
};
```

## 配置 index.html 模板

- 生成多个 HTML 文件

要生成多个 HTML 文件，请在插件数组中多次声明插件

**webpack.config.js**

```js
module.exports = {
  entry: "index.js",
  output: {
    path: __dirname + "/dist",
    filename: "index_bundle.js",
  },
  plugins: [
    new HtmlWebpackPlugin(), //Generatres default index.html
    new HtmlWebpackPlugin({
      //also generate a test.html
      filename: "test.html",
      template: "src/assets/test.html",
    }),
  ],
};
```

- 编写自己的模板
  如果默认生成的 HTML 不能满足您的需要，您可以提供自己的模板。最简单的方法是使用 template 选项并传递自定义 HTML 文件。html 网页包插件将自动将所有必要的 CSS、JS、manifest 和 favicon 文件注入标记中。

```js
module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      title: "Custom template",
      // Load a custom template (lodash by default)
      template: "index.html",
    }),
  ],
};
```

## 多页面应用

**webpack.config.js**

```js
module.exports = {
  entry: {
    pageOne: "./src/pageOne/index.js",
    pageTwo: "./src/pageTwo/index.js",
    pageThree: "./src/pageThree/index.js",
  },
};
```
这是什么？ 我们告诉 webpack 需要三个独立分离的依赖图（如上面的示例）。

为什么？ 在多页面应用程序中，server 会拉取一个新的 HTML 文档给你的客户端。页面重新加载此新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很多事，例如使用 `optimization.splitChunks` 为页面间共享的应用程序代码创建bundle。由于入口起点数量的增多，多页应用能够复用多个入口起点之间的大量代码/模块，从而可以极大地从这些技术中受益。
