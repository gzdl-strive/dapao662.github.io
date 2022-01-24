---
title: Babel
toc: true
date: 2021-11-03 15:53:02
---

- 经过一系列的配置，使得 TS 和 webpack 已经结合到了一起，除了 webpack，开发中还经常需要结合 babel 来对代码进行转换以使其可以兼容到更多的浏览器，在上述步骤的基础上，通过以下步骤再将 babel 引入到项目中。

1. 安装依赖包：

- `npm i -D @babel/core @babel/preset-env babel-loader core-js`
- 共安装了 4 个包，分别是：
  - @babel/core
    - babel 的核心工具
  - @babel/preset-env
    - babel 的预定义环境
  - @babel-loader
    - babel 在 webpack 中的加载器
  - core-js
    - core-js 用来使老版本的浏览器支持新版 ES 语法

2. 修改 webpack.config.js 配置文件

```javascript
// ...略...
module: {
  rules: [
    {
      test: /\.ts$/,
      use: [
        // 配置babel
        {
          // 指定加载器
          loader: "babel-loader",
          //设置babel
          options: {
            //设置预定义的环境
            presets: [
              [
                //指定环境的插件
                "@babel/preset-env",
                //配置信息
                {
                  //要兼容的目标浏览器
                  targets: {
                    chrome: "58",
                    ie: "11",
                  },
                  //指定corejs的版本
                  corejs: "3",
                  //使用corejs的方式 "usage"表示按需加载
                  useBuiltIns: "usage",
                },
              ],
            ],
          },
        },
        {
          loader: "ts-loader",
        },
      ],
      exclude: /node_modules/,
    },
  ];
}
// ...略...
```

- 如此一来，使用 ts 编译后的文件将会再次被 babel 处理，使得代码可以在大部分浏览器中直接使用，可以在配置选项的 targets 中指定要兼容的浏览器版本。
