---
title: HMR热模块替换
date: 2020-11-03 15:19:24
tag: webpack
---

## HMR: hot module replacement 热模块替换/模块热替换
>作用：一个模块发生变化，只会重新打包这一个模块（而不是打包所以模块）极大提升构建速度
* 样式文件：可以使用`HMR`功能-->因为`style-loader`内部实现了
* `js`文件：默认不能使用`HMR`功能-->(需要修改`js`代码,添加支持`HMR`功能的代码)**注意**：`HMR`功能对`js`的处理，只能处理非入口`js`文件的其他文件，（因为一旦入口文件改变了，其他文件也会变）
* `html`文件：默认不能使用`HMR`功能,同时会导致问题：`html`文件不能热更新了解决:修改`entry`入口,将`html`文件引入--还是使用不了`HMR`功能(不用`HMR`功能，只有一个`html`)

```js
//webpack.config.js
const {resolve} = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry:['./src/js/index.js','./src/index.html'],
    output:{
        filename:'js/built.js',
        path:resolve(__dirname,'build')
    },
    module:{
        rules:[
            //loader的配置
            {
                //处理less资源
                test:/\.less$/,
                use:['style-loader','css-loader','less-loader']
            },
            {
                //处理css资源
                test:/\.css$/,
                use:['style-loader','css-loader']
            },
            {
                //处理图片资源
                test:/\.(jpg|png|gif)/,
                loader:'url-loader',
                options:{
                    limit:8*1024,
                    name:'[hash:10].[ext]',
                    outputPath:'imgs'
                }
            },
            {
                //处理html中的图片资源
                test:/\.html$/,
                loader:'html-loader',
            },
            {
                //处理其他资源
                exclude:/\.(less|css|js|jpg|png|gif|html)/,
                loader:'file-loader',
                options:{
                    name:'[hash:10].[ext]',
                    outputPath:'media'
                }
            }
        ]
    },
    plugins:[
        //plugins的配置
        new HtmlWebpackPlugin({
            template:'./src/index.html'
        })
    ],
    mode:'development',
    devServer:{
        contentBase:resolve(__dirname,'build'),
        compress:true,
        port:3000,
        open:true,
        //开启HMT功能
        //当修改了webpack配置，新配置想要生效，必须重新webpack服务
        hot:true
    }
}

```

**index.js中(自己写)使用HMR**
```js
import '../style/iconfont.css';
import '../style/index.less';
import print from './print';

print();
function add(x,y){
    return x+y;
}
console.log('index.js'+add(2,3));

if (module.hot){
    //一旦module.hot为true,说明开启了HMR功能-->让HMR功能代码生效
    module.hot.accept('./print.js',function(){
        //方法会监听print.js文件的变化，一旦发生变化，其他模块不会重新打包构建。
        //会执行后面的回调函数
        print();
    })
}
```