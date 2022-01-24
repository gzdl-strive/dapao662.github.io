---
title: 管理资源
date: 2021-12-21 21:51:24
toc: true
---

在上一章，我们讲解了四种资源模块引入外部资源。除了资源模块，我们还可以通过 loader 引入其他类型的文件

### 什么是 loader

webpack 只能理解 `JavaScript` 和 `JSON` 文件，这是 webpack 开箱可用的自带能力。
**loader** 让 webpack 能够去处理其他类型的文件，并将它们转换为有效模块，以供应用程序使用，以及被添加到依赖图中。

在 webpack 的配置中，loader 有两个属性：

1. test 属性，识别出哪些文件会被转换。
2. use 属性，定义出在进行转换时，应该使用哪个 loader。

```js
//webpack.config.js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.txt$/,
        use: "raw-loader",
      },
    ],
  },
};
```

以上配置中，对一个单独的 module 对象定义了 rules 属性，里面包含两个必须属性： test 和 use 。这告诉 webpack 编译器(compiler) 如下信息：

> “嘿，webpack 编译器，当你碰到「在 require() / import 语句中被解析为'.txt' 的路径」时，在你对它打包之前，先 use(使用) raw-loader 转换一下。”

### 加载 CSS

为了在 JavaScript 模块中 import 一个 CSS 文件，你需要安装 style-loader 和 css-loader，并在 module 配置 中添加这些 loader：

```powershell
npm install --save-dev style-loader css-loader
```

修改配置文件

```js
module.exports = {
  //..
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

模块 loader 可以链式调用。链中的每个 loader 都将对资源进行转换。链会逆序执行。第一个 loader 将其结果（被转换后的资源）传递给下一个 loader，依此类推。最后，webpack 期望链中的最后的 loader 返回 JavaScript。

应保证 loader 的先后顺序： 'style-loader' 在前，而 'css-loader' 在后。如果不遵守此约定，webpack 可能会抛出错误。webpack 根据正则表达式，来确定应该查找哪些文件，并将其提供给指定的 loader。在这个示例中，所有以 .css 结尾的文件，都将被提供给 style-loader 和 css-loader 。

这使你可以在依赖于此样式的 js 文件中 import './style.css' 。现在，在此模块执行过程中，含有 CSS 字符串的 `<style>` 标签，将被插入到 html 文件的 `<head>`中。

我们尝试一下，通过在项目中添加一个新的 style.css 文件，并将其 import 到我们的 index.js 中：
![css,style](/assets/engineeringImg/webpackImg/cssLoader.png "css/style - loader")

```css
.hello {
  color: #f9efd4;
}
```

在入口文件中导入`.css`文件

```js
//src/index.js
import "./style.css";
document.body.classList.add("hello");
```

启动服务`npx webpack serve --open`
![css,style2](/assets/engineeringImg/webpackImg/cssLoader2.png "css/style - loader2")

你应该看到页面背景颜色是浅黄色。要查看 webpack 做了什么，请检查页面（不要查看页面源代码，它不会显示结果，因为 `<style>` 标签是由 JavaScript 动态创建的），并查看页面的 head 标签，包含 style 块元素，也就是我们在 index.js 中 import 的 css 文件中的样式。

现有的 loader 可以支持任何你可以想到的 CSS 风格 - sass 和 less 等。安装 less-loader：

```powershell
npm install less less-loader --save-dev
```

```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.less$/i,
        use: ["style-loader", "css-loader", "less-loader"],
      },
    ],
  },
};
```

在项目 src 目录下创建 style.less 文件：

```less
@color: red;
.world {
  color: @color;
}
```

在入口文件中引入 .less 文件：

```js
//index.js
import "./style.less";
document.body.classList.add("world");
```

启动服务可见，页面的文字都添加了“红色”的样式。

### 抽离和压缩 CSS

在多数情况下，我们也可以进行压缩 CSS，以便在生产环境中节省加载时间，同时还可以将 CSS 文件抽离成一个单独的文件。实现这个功能，需要 `mini-css-extract- plugin` 这个插件来帮忙。安装插件：

```powershell
npm install mini-css-extract-plugin --save-dev
```

本插件会将 CSS 提取到单独的文件中，为每个包含 CSS 的 JS 文件创建一个 CSS 文件，并且支持 CSS 和 SourceMaps 的按需加载。
本插件基于 webpack v5 的新特性构建，并且需要 webpack 5 才能正常工作。
之后将 loader 与 plugin 添加到你的 webpack 配置文件中：

```js
//webpack.config.js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
  plugins: [new MiniCssExtractPlugin()],
};
```

执行编译
![miniCSS](/assets/engineeringImg/webpackImg/miniCss.png "miniCssExtractPlugin")

单独的 `mini-css-extract-plugin` 插件不会将这些 CSS 加载到页面中。这里 `html- webpack-plugin` 帮助我们**自动生成 link 标签**或者**在创建 index.html 文件时使 用 link 标签**。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial- scale=1.0" />
    <title>webpack app</title>
    <link href="main.css" rel="stylesheet" />
  </head>
  <body>
    <script defer src="bundle.js"></script>
  </body>
</html>
```

这时， link 标签已经生成出来了，把我们打包好的 main.css 文件加载进来。我们发现， main.css 文件被打包抽离到 dist 根目录下，能否将其打包到一个单独的文件夹里呢？修改配置文件:
```js
module.exports = {
  //...
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'styles/[contenthash].css'
    })
  ]
}
```
再次执行编译,发现css文件已经抽离到styles目录下了

>打开查看.css文件，发现，文件并没有被压缩和优化
为了压缩输出文件，使用类似于`css-minimizer-webpack-plugin`这样的插件。安装插件
```powershell
npm install css-minimizer-webpack-plugin --save-dev
```

```js
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
//webpack.config.js
module.exports = {
  //开启生产模式(测试环境没必要开启压缩)
  mode: 'production',
  //优化配置
  optimization: {
    minimizer: [
      new CssMinimizerPlugin(),
    ]
  }
}
```
再次执行编译，css文件优化成功

### 加载images图像
假如，现在我们正在下载 CSS，但是像 background 和 icon 这样的图像，要如何处理呢？在 webpack 5 中，可以使用内置的 Asset Modules，我们可以轻松地将这些内容混入我们的系统中，这个我们在"资源模块"一节中已经介绍了。这里再补充一个知识点，在 css 文件里也可以直接引用文件，修改 style.css 和入口 index.js
```css
.block-bg {
  background-image: url(./assets/webpack.logo.svg);
}
```
```js
block.style.cssText = `width: 200px; height: 200px; background-color: #2b3a42`;
block.classList.add('block-bg');
```
我们看到，通过样式把背景图片加到了页面中。

### 加载fonts字体
那么，像字体这样的其他资源如何处理呢？使用 Asset Modules 可以接收并加载任何文件，然后将其输出到构建目录。这就是说，我们可以将它们用于任何类型的文件，也包括字体。让我们更新 `webpack.config.js` 来处理字体文件：
```js
//webpack.config.js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/i,
        type: 'asset/resource',
      }
    ]
  }
}
```
在项目中添加一些字体文件：
![fonts](/assets/engineeringImg/webpackImg/assets_fonts.png "加载字体")

配置好loader并将字体文件放在合适的位置后，你可以通过一个`@font-face`声明将其混合。本地的`url(...)`指令会被webpack获取处理，就像它处理图片一样:

```css
@font-face {
  font-family: 'iconfont';
  src: url('./assets/iconfont.ttf').format('truetype');
}

.icon {
  font-family: "iconfont" !important;
  font-size: 30px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

```js
//index.js
const span = document.createElement('span'); span.classList.add('icon'); 
span.innerHTML = '&#xe668'; 
document.body.appendChild(span);
```

执行编译
![fonts2](/assets/engineeringImg/webpackImg/assets_fonts2.png "加载字体")

### 加载数据
此外，可以加载的有用资源还有数据，如 JSON 文件，CSV、TSV 和 XML。类似于NodeJS，JSON 支持实际上是内置的，也就是说 import Data from './data.json' 默认将正常运行。要导入 CSV、TSV 和 XML，你可以使用 csv-loader 和 xml-loader。让我们处理加载这三类文件：
```powershell
npm install --save-dev csv-loader xml-loader
```

修改配置文件
```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.(csv|tsv)$/i,
        use: ['csv-loader'],
      },
      {
        test: /\.xml$/i,
        use: ['xml-loader'],
      }
    ]
  }
}
```
现在，你可以 import 这四种类型的数据(JSON, CSV, TSV, XML)中的任何一种，所导入的 Data 变量，将包含可直接使用的已解析 JSON

### 自定义JSON模块parser
通过使用 自定义 parser 替代特定的 webpack loader，可以将任何 toml 、 yaml 或 json5 文件作为 JSON 模块导入。

假设你在 src 文件夹下有一个 data.toml 、一个 data.yaml 以及一个data.json5 文件

首先安装 toml ， yamljs 和 json5 的 packages：
```powershell
npm install toml yamljs json5 --save-dev
```

修改配置文件
```js
const toml = require('toml'); 
const yaml = require('yamljs'); 
const json5 = require('json5');

module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.toml$/i,
        type: 'json',
        parser: {
          parse: toml.parse,
        },
      },
      {
        test: /\.yaml$/i,
        type: 'json',
        parser: {
          parse: yaml.parse,
        },
      },
      {
        test: /\.json5$/i,
        type: 'json',
        parser: {
          parse: json5.parse,
        }
      }
    ]
  }
}
```

