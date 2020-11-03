---
title: webpack配置详解
date: 2020-11-03 21:39:24
tag: webpack
toc: true
---

## entry
```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
/*
    entry: 入口起点
        1、string --> './src/index.js'
            打包形成一个chunk.输出一个bundle文件
            此时chunk的名称默认是main
        2、array -->['./src/index.js','./src/add.js']
            多入口
            所有入口文件最终只会形成一个chunk,输出出去只有一个bundle文件
                --> 只有在HMR功能中让html热更新生效~
        3、object-->{index: './src/index.js',add: './src/add.js'}
            多入口
            有几个入口文件就形成几个chunk,输出几个bundle文件
            此时chunk的名称是key
        
        -->特殊用法entry:{
            //所有入口文件最终只会形成一个chunk,输出出去只有一个bundle文件
            index: ['./src/index.js','./src/count.js'],
            //形成一个chunk，输出一个bundle文件
            add: './src/add.js'
        }
*/
module.exports = {
    //entry: './src/index.js',
    //entry: ['./src/index.js','./src/add.js'],
    entry:{
        index: './src/index.js',
        add: './src/add.js'
    },
    output: {
        filename: '[name].js',
        path: resolve(__dirname, 'build')
    },
    plugins: [
        new HtmlWebpackPlugin()
    ],
    mode: 'development'
}
```

## output
```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        //文件名称(指定目录和名称)
        filename: 'js/[name].js',
        //输出的文件目录(将来所有资源输出的公共目录)
        path: resolve(__dirname, 'build'),
        //所有资源引入的公共路径的前缀 --> 'imgs/a.jpg' --> '/imgs/a.jpg'
        publicPath: '/',
        //非入口chunk的名称--（import语法和optim..）
        chunkFilename: 'js/[name]_chunk.js',
        //整个库向外暴露的变了名
        library:'[name]',
        //变量名添加到哪个上browser
        //libraryTarget: 'browser',
        //变量名添加到哪个上node
        //libraryTarget: 'global',
        libraryTarget: 'commonjs',
    },
    plugins: [
        new HtmlWebpackPlugin()
    ],
    mode: 'development'
}
```

## modules
```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        filename: 'js/[name].js',
        path: resolve(__dirname, 'build'),
    },
    module: {
        rules: [
            //loader的配置
            {
                test:/\.css$/,
                //多个Loader用use
                use: ['style-loader','css-loader']
            },
            {
                test:/\.js$/,
                //排除node_modules下的js文件
                exclude:/node_modules/,
                //值检查src下的js文件
                include:resolve(__dirname,'src'),
                //优先执行enforce: 'pre'--不写中间执行
                enforce: 'pre',
                //延后执行enforce: 'post'
                //单个loader用loader
                loader:'eslint-loader',
                options:{}
            },
            {
                //以下配置只会生效一个
                oneOf: []
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin()
    ],
    mode: 'development'
}
```

## resolve
```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/[name].js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development',
  // 解析模块的规则
  resolve: {
    // 配置解析模块路径别名: 优点简写路径 缺点路径没有提示
    alias: {
      $css: resolve(__dirname, 'src/css')
    },
    // 配置省略文件路径的后缀名
    extensions: ['.js', '.json', '.jsx', '.css'],
    // 告诉 webpack 解析模块是去找哪个目录
    modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
  }
}
```

## dev_server
```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/[name].js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development',
  resolve: {
    alias: {
      $css: resolve(__dirname, 'src/css')
    },
    extensions: ['.js', '.json', '.jsx', '.css'],
    modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
  },
  devServer: {
    // 运行代码的目录
    contentBase: resolve(__dirname, 'build'),
    // 监视 contentBase 目录下的所有文件，一旦文件变化就会 reload
    watchContentBase: true,
    watchOptions: {
      // 忽略文件
      ignored: /node_modules/
    },
    // 启动gzip压缩
    compress: true,
    // 端口号
    port: 5000,
    // 域名
    host: 'localhost',
    // 自动打开浏览器
    open: true,
    // 开启HMR功能
    hot: true,
    // 不要显示启动服务器日志信息
    clientLogLevel: 'none',
    // 除了一些基本启动信息以外，其他内容都不要显示
    quiet: true,
    // 如果出错了，不要全屏提示~
    overlay: false,
    // 服务器代理 --> 解决开发环境跨域问题
    proxy: {
      // 一旦devServer(5000)服务器接受到 /api/xxx 的请求，就会把请求转发到另外一个服务器(3000)
      '/api': {
        target: 'http://localhost:3000',
        // 发送请求时，请求路径重写：将 /api/xxx --> /xxx （去掉/api）
        pathRewrite: {
          '^/api': ''
        }
      }
    }
  }
}
```

## optimization
```js
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/[name].js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [new HtmlWebpackPlugin()],
  mode: 'development',
  resolve: {
    alias: {
      $css: resolve(__dirname, 'src/css')
    },
    extensions: ['.js', '.json', '.jsx', '.css'],
    modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
  },
  devServer: {
    // 运行代码的目录
    contentBase: resolve(__dirname, 'build'),
    // 监视 contentBase 目录下的所有文件，一旦文件变化就会 reload
    watchContentBase: true,
    watchOptions: {
      // 忽略文件
      ignored: /node_modules/
    },
    // 启动gzip压缩
    compress: true,
    // 端口号
    port: 5000,
    // 域名
    host: 'localhost',
    // 自动打开浏览器
    open: true,
    // 开启HMR功能
    hot: true,
    // 不要显示启动服务器日志信息
    clientLogLevel: 'none',
    // 除了一些基本启动信息以外，其他内容都不要显示
    quiet: true,
    // 如果出错了，不要全屏提示~
    overlay: false,
    // 服务器代理 --> 解决开发环境跨域问题
    proxy: {
      // 一旦devServer(5000)服务器接受到 /api/xxx 的请求，就会把请求转发到另外一个服务器(3000)
      '/api': {
        target: 'http://localhost:3000',
        // 发送请求时，请求路径重写：将 /api/xxx --> /xxx （去掉/api）
        pathRewrite: {
          '^/api': ''
        }
      }
    }
  }
}
```