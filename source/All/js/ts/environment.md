---
title: 项目环境
toc: true
date: 2021-11-03 16:13:02
---

**1、初始化文件夹**

```powershell
npm init -y
```

生成 package.json 文件

**2、下载构建工具**

```powershell
# 下载webpack工具以及插件
yarn add webpack webpack-cli webpack-dev-server typescript ts-loader html-webpack-plugin clean-webpack-plugin --save-dev
# 下载babel工具
yarn add @babel/core @babel/preset-env babel-loader core-js --save-dev
# 下载样式插件,使用less
yarn add less less-loader css-loader style-loader --save-dev
# 下载兼容低版本样式插件
yarn add postcss postcss-loader postcss-preset-env --save-dev
```

**3、根目录下创建 webpack 的配置文件 webpack.config.js**

```js
// 引入一个包
const path = require("path");
// 引入html插件
const HTMLWebpackPlugin = require("html-webpack-plugin");
// 引入clean插件
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

// webpack中的所有的配置信息都应该写在module.exports中
module.exports = {
  // 指定入口文件
  entry: "./src/index.ts",

  // 指定打包文件所在目录
  output: {
    // 指定打包文件的目录
    path: path.resolve(__dirname, "dist"),
    // 打包后文件的文件
    filename: "bundle.js",

    // 告诉webpack不使用箭头
    environment: {
      arrowFunction: false,
      const: false, //不使用const
    },
  },

  mode: "production",

  // 指定webpack打包时要使用模块
  module: {
    // 指定要加载的规则
    rules: [
      {
        // test指定的是规则生效的文件
        test: /\.ts$/,
        // 要使用的loader
        use: [
          // 配置babel
          {
            // 指定加载器
            loader: "babel-loader",
            // 设置babel
            options: {
              // 设置预定义的环境
              presets: [
                [
                  // 指定环境的插件
                  "@babel/preset-env",
                  // 配置信息
                  {
                    // 要兼容的目标浏览器
                    targets: {
                      chrome: "58",
                      ie: "11",
                    },
                    // 指定corejs的版本
                    corejs: "3",
                    // 使用corejs的方式 "usage" 表示按需加载
                    useBuiltIns: "usage",
                  },
                ],
              ],
            },
          },
          "ts-loader",
        ],
        // 要排除的文件
        exclude: /node-modules/,
      },
      //设置less文件的处理
      {
        test: /\.less$/,
        use: [
          "style-loader",
          "css-loader",
          //引入postcss
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  [
                    "postcss-preset-env",
                    {
                      browsers: "last 2 versions",
                    },
                  ],
                ],
              },
            },
          },
          "less-loader",
        ],
        exclude: /node-modules/,
      },
    ],
  },

  // 配置Webpack插件
  plugins: [
    new CleanWebpackPlugin(),
    new HTMLWebpackPlugin({
      // title: "这是一个自定义的title"
      template: "./src/index.html",
    }),
  ],

  // 用来设置引用模块
  resolve: {
    extensions: [".ts", ".js"],
  },
};
```

**4、根目录下创建 tsconfig.json**

```json
{
  "compilerOptions": {
    "module": "ES2015",
    "target": "ES2015",
    "strict": true,
    "noEmitOnError": true
  }
}
```

**5、修改 package.json**

```json
{
  // ...
  "scripts": {
    "build": "webpack",
    "start": "webpack serve --open chrome.exe"
  }
  // ...
}
```
