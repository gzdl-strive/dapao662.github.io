---
title: 资源模块
date: 2021-12-21 21:18:24
toc: true
---

目前为止，我们的项目可以在控制台上显示 "Hello world~~~"。现在我们尝试混合一些其他资源，比如 images，看看 webpack 如何处理.

在 webpack 出现之前，前端开发人员会使用 grunt 和 gulp 等工具来处理资源，并将它们从 /src 文件夹移动到 /dist 或 /build 目录中。webpack 最出色的功能之一就是，除了引入 JavaScript，还可以内置的资源模块`Asset Modules`引入任何其他类型的文件。

资源模块(asset module)是一种模块类型，它允许我们应用 Webpack 来打包其他资源文件（如字体，图标等）

资源模块类型(asset module type)，通过添加 4 种新的模块类型，来替换所有这些 loader:

- asset/resource 发送一个单独的文件并导出 URL。
- asset/inline 导出一个资源的 data URI。
- asset/source 导出资源的源代码。
- asset 在导出一个 data URI 和发送一个单独的文件之间自动选择。

### Resource 资源

修改 webpack.config.js 配置

```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.png/,
        type: "asset/resource",
      },
    ],
  },
  //...
};
```
准备资源文件，在入口文件中引入，并显示在页面上：
![resource](/assets/engineeringImg/webpackImg/assets_resource.png "resource")

修改src/index.js文件
```js
// 导入函数模块 
import helloWorld from './hello-world.js' import imgsrc from './assets/img-1.png' helloWorld() 
const img = document.createElement('img'); 
img.src = imgsrc;
document.body.appendChild(img);
```
执行打包命令

发现图片(.png)文件已经打包到dist目录下：

![resource2](/assets/engineeringImg/webpackImg/assets_resource2.png "resource2")

执行启动服务命令:`npx webpack serve --open`等价于`npx webpack-dev-server --open`

打开浏览器，发现图片已经出现在页面上了

- 自定义输出文件名
默认情况下， `asset/resource` 模块以 `[contenthash][ext][query]` 文件名发送到输出目录。
可以通过在 webpack 配置中设置 `output.assetModuleFilename` 来修改此模板字符串：
```js
module.exports = {
  //...
  output: {
    assetModuleFilename: 'images/[contenthash][ext][query]'
  }
  //...
}
```
执行编译，图片就会被打包到dist/images下

- 另一种自定义输出文件名的方式是，将某些资源发送到指定目录,修改配置
```js
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.png$/,
        type: 'asset/resource',
        //优先级高于assetModuleFilename
        generator: {
          filename: 'images/[contenthash][ext][query]'
        }
      }
    ]
  }
  //...
}
```

### inline 资源
修改webpack.config.js配置
```js
module.exports = {
  //...
  module: {
    rules: [
      //...
      {
        test: /\.svg$/,
        type: 'asset/inline'
      }
    ]
  }
  //...
}
```
启动服务，查看控制台svg的元素，发现.svg文件都将作为data URI注入到bundle中
![inline](/assets/engineeringImg/webpackImg/assets_inline.png "inline")

- 自定义data URI生成器
webpack 输出的 data URI，默认是呈现为使用 Base64 算法编码的文件内容。如果要使用自定义编码算法，则可以指定一个自定义函数来编码文件内容:
安装自定义函数模块：
```powershell
npm install mini-svg-data-uri -D
```
修改配置文件
```js
const svgToMiniDataURI = require('mini-svg-data-uri');
module.exports = {
  //...
  module: {
    rules: [
      //...
      {
        test: /\.svg$/,
        type: 'asset/inline',
        generator: {
          dataUrl: content => {
            content = content.toString();
            return svgToMiniDataURI(content);
          }
        }
      }
    ]
  }
}
```
现在，所有 .svg 文件都将通过 `mini-svg-data-uri` 包进行编码。重新启动服务，在浏览器查看效果：

![inline2](/assets/engineeringImg/webpackImg/assets_inline2.png "inline2")

### source 资源
source资源，导出资源的源代码。修改配置文件，添加:
```js
module.exports = {
  //...
  module: {
    rules: [
      //...
      {
        test: /\.txt$/,
        type: 'asset/source',
      }
    ]
  }
  //...
}
```
在assets里创建一个 example.txt 文件：
```txt
hello webpack
```

在入口文件中引入一个`.txt`文件，添加内容
```js
//src/index.js
import exampleText from './assets/example.txt';
const block = document.createElement('div');
block.style.cssText = `width: 200px; height: 200px; background: aliceblue`; 
block.textContent = exampleText 
document.body.appendChild(block)
```

启动服务，发现出现文字，所有.txt文件都将原样注入到bundle中

### 通用资源类型
通用资源类型 asset , 在导出一个 data URI 和发送一个单独的文件之间自动选择。修改配置文件:
```js
module.exports = {
  //...
  module: {
    rules: [
      //...
      {
        test: /\.jpg$/,
        type: 'asset'
      }
    ]
  }
}
```

现在，webpack 将按照默认条件，自动地在 resource 和 inline 之间进行选择：小于 8kb 的文件，将会视为 inline 模块类型，否则会被视为 resource 模块类型。

可以通过在 webpack 配置的 `module rule` 层级中，设置`Rule.parser.dataUrlCondition.maxSize` 选项来修改此条件:
```js
module.exports = {
  //...
  module: {
    rules: [
      //...
      {
        test: /\.jpg$/,
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 4 * 1024, //4kb
          }
        }
      }
    ]
  }
}
```



![inline2](/assets/engineeringImg/webpackImg/assets_inline2.png "inline2")
