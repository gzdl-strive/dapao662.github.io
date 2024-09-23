---
title: webpack开发环境配置
date: 2020-11-03 09:31:16
tag: webpack
---
> `webpack.config.js` --`webpack`的配置文件
作用：指示`webpack`干哪些活(当你运行`webpack`指令时，会加载里面的配置)
所有的构建工具都是基于`nodejs`平台运行的~模块化默认采用`commonjs.`

>开发环境配置：能让代码运行即可
运行项目指令：
`webpack` 会将打包结果输出出去
`npx webpack-dev-server` 只会在内存中编译打包，没有输出

## 1、打包样式资源
```js
//webpack.config.js
const { resolve } = require('path');
//resolve用来拼接绝对路径的方法
const HtmlWebpackPlugin = require('html-webpack-plugin');
//

module.exports = {
    //webpack配置
    //入口起点
    entry: './src/js/index.js',
    //输出
    output: {
        //输出文件名
        filename: 'js/built.js',
        //输出路径
        //__dirname nodejs的变量，代表当前文件的目录绝对路径
        path: resolve(__dirname, 'build')
    },
    //loader的配置
    modules: {
        rules: [
            //不同文件必须配置不同的loader处理
            {
                //处理less资源
                test: /\.less$/,
                //use数组中loader执行顺序：从右到左，从下到上，依次执行
                use: [
                    //创建style标签，将js中的样式资源插入进行，添加到head中生效
                    'style-loader',
                    //将css文件变成commonjs模块加载js中,里面内容是样式字符串
                    'css-loader',
                    //将less文件编译成css文件
                    //需要下载less-loader和less
                    'less-loader'
                ]
            },
            {
                //处理css资源
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                //问题：默认处理不了html中的img图片
                //处理图片资源
                test: /\.(jpg|png|gif)/,
                //使用一个loader
                //下载url-loader file-loader
                loader: 'url-loader',
                options: {
                    //图片大小小于8kb，就会被base64处理
                    //优点:减少请求数量（减轻服务器压力）
                    //缺点：图片体积会更大(文件请求速度更慢)
                    limit: 8 * 1024,
                    //给图片进行重命名
                    //[hash:10]取图片的hash的前10位
                    //[ext]取文件原来的扩展名
                    name: '[hash:10].[ext]',
                    outputPath: 'imgs'
                }
            },
            {
                test: /\.html$/,
                //处理html文件的img图片(负责引入img,从而能被url-loader进行处理)
                loader: 'html-loader'
            },
            {
                //处理其他资源
                exclude: /\.(less|css|js|jpg|png|gif|html)/,
                loader: 'file-loader',
                options: {
                    name: '[hash:10].[ext]',
                    outputPath: 'media'
                }
            }
        ]
    },
    plugins: [
        //plugins的配置
        //html-webpack-plugin
        //功能:默认会创建一个空的HTML,自动引入打包输出的所有资源(JS/CSS)
        //需要有结果的HTML文件       
        new HtmlWebpackPlugin({
            //复制'./src/index.html'文件并自动引入打包输出的所有资源(JS/CSS)
            template: './src/index.html'
        })
    ],
    mode: 'development',
    //开发服务器devServer:用来自动化(自动编译，自动打开浏览器，自动刷新~~)
    //特点：只会在内存中编译打包，不会有任何输出
    //下载包npm install webpack-dev-server
    //因为是本地安装--使用npx启动--而webpack全局安装了也本地安装了所以不需要npx
    //启动devServer指令为:npx webpack-dev-server
    devServer: {
        //项目构建后路径
        contentBase: resolve(__dirname, 'build'),
        //启动gzip压缩
        compress: true,
        //端口号
        prot: 3000,
        //自动打开浏览器
        opent: true
    }
}
```
