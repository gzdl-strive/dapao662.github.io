---
title: CommonJS
date: 2021-04-04 14:29:20
tag: js
toc: true
---

### 概述
>Node应用由模块组成，采用CommonJS模块规范。每个文件就是一个模块，有自己的作用域。在一个文件里面定义的变量、函数、类，都是私有的，对其他文件不可见。在服务器端，模块的加载是运行时同步加载的；在浏览器端，模块需要提前编译打包处理。

### 特点
1. 所有代码都运行在模块作用域，不会污染全局作用域。
2. 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
3. 模块加载的顺序，按照其在代码中出现的顺序。

### 基本语法
* 暴露模块：`module.exports = value 或 exports.xxx = value`
* 引入模块：`require(xxx)`,如果是第三方模块，xxx为模块名；如果是自定义模块，xxx为模块文件路径

此处我们有个疑问：**CommonJS暴露的模块到底是什么?**
>CommonJS规范规定，每个模块内部，module变量代表当前模块。这个模块是一个对象，它的exports属性(即module.exports)是对外的接口。
加载某个模块，其实是加载该模块的module.exports属性.
```js
    // example.js
    var x = 5;
    var addX = function (value) {
    return value + x;
    };
    module.exports.x = x;
    module.exports.addX = addX;
    //上面代码通过module.exports输出变量x和函数addX。
    var example = require('./example.js');//如果参数字符串以“./”开头，则表示加载的是一个位于相对路径
    console.log(example.x); // 5
    console.log(example.addX(1)); // 6
```
require命令用于加载模块文件。require命令的基本功能是，读入并执行一个JavaScript文件，然后返回该模块的exports对象。如果没有发现指定模块，会报错。

### 模块的加载机制
CommonJS模块的加载机制是，**输入的是被输出的值的拷贝**。也就是所，一旦输出一个值，模块内部的变化就影响不到这个值。
这点和ES6模块化由重大差异，请看下面这个例子:
```js
    // lib.js
    var counter = 3;
    function incCounter() {
    counter++;
    }
    module.exports = {
    counter: counter,
    incCounter: incCounter,
    };
    //上面代码输出内部变量counter和改写这个变量的内部方法incCounter。
    // main.js
    var counter = require('./lib').counter;
    var incCounter = require('./lib').incCounter;

    console.log(counter);  // 3
    incCounter();
    console.log(counter); // 3
```
上面代码说明，counter输出以后，lib.js模块内部的变化就影响不到counter了。**这是因为counter是一个原始类型的值，会被缓存。除非写成一个函数，才能得到内部变动后的值。**

### 服务器端实现
1. 下载安装node.js
2. 创建项目目录
注意：用`npm init `自动生成package.json时，package name(包名)不能有中文和大写
>|-app.js
>|-package.json
>|-modules
>>|-module1.js
>>|-module2.js
>>|-module3.js

```js
{
    "name": "commonJS-node",
    "version": "1.0.0"
}
```
3. 下载第三方模块
`npm install uniq --save` // 用于数组去重
4. 定义模块代码
```js
//module1.js
module.exports = {
    msg: 'module1',
    foo() {
        console.log(this.msg)
    }
}
//module2.js
module.exports = {
    console.log('module2');
}
//module3.js
exports.foo = function () {
    console.log('foo() module3');
}
exports.arr = [1, 2, 3, 3, 4, 4];
// app.js文件
// 引入第三方库，应该放置在最前面
let uniq = require('uniq')
let module1 = require('./modules/module1')
let module2 = require('./modules/module2')
let module3 = require('./modules/module3')

module1.foo() //module1
module2() //module2
module3.foo() //foo() module3
console.log(uniq(module3.arr)) //[ 1, 2, 3, 4 ]
```
5. 通过node运行app.js
命令行输入`node app.js`，运行js文件

### 浏览器端实现(实现Browserify)
1. 创建项目结构
```js
|-js
|-dist //打包生成文件的目录
|-src //源码所在的目录
    |-module1.js
    |-module2.js
    |-module3.js
    |-app.js //应用主源文件
|-index.html //运行于浏览器上
|-package.json
```
```js
{
    "name": "browserify-test",
    "version": "1.0.0"
}
```
2. 下载browserify
* 全局 npm install browserify -g
* 局部 npm install browserify --save-dev

3. 定义模块代码(同服务器端)
注意：Index.html文件要运行在浏览器端，需要借助browserify将app.js文件打包编译，如果直接在index.html引入app.js就会报错
4. 打包处理
根目录下运行`browserify js/src/app.js -o js/dist/bundle.js`
5. 页面引入使用
在index.html文件中引入`<script src="js/dist/bundle.js"></script>`


