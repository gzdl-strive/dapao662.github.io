---
title: AMD
date: 2021-04-04 14:38:20
tag: js
toc: true
---

CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。
AMD规范则是非同步加载模块，允许指定回调函数。由于node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，
所以CommonJs规范比较适用，但是，如果**是浏览器环境，要从服务端加载模块，这是就必须采用非同步模块，因此浏览器端一般采用AMD规范**
此外AMD规范比CommonJS规范在浏览器端实现要来的早
### (1)AMD规范基本语法
定义暴露模块:
```js
//定义没有依赖的模块
define(function() {
    return 模块
})
//定义有依赖的模块
define(['module1', 'module2'], function(m1, m2) {
    return 模块
})
// 引入使用模块:
require(['module1', 'module2'], function(m1, m2) {
    使用m1, m2
})
```
### (2)未使用AMD规范与使用require.js
通过比较两者的实现方法，来说明使用AMD规范的好处
#### 1.未使用AMD规范
```js
// dataService.js文件
(function (window) {
let msg = 'www.baidu.com'
function getMsg() {
    return msg.toUpperCase()
}
window.dataService = {getMsg}
})(window)
// alerter.js文件
(function (window, dataService) {
let name = 'Tom'
function showMsg() {
    alert(dataService.getMsg() + ', ' + name)
}
window.alerter = {showMsg}
})(window, dataService)
// main.js文件
(function (alerter) {
alerter.showMsg()
})(alerter)
// index.html文件
<div><h1>Modular Demo 1: 未使用AMD(require.js)</h1></div>
<script type="text/javascript" src="js/modules/dataService.js"></script>
<script type="text/javascript" src="js/modules/alerter.js"></script>
<script type="text/javascript" src="js/main.js"></script>
```
![noAmd](/assets/jsImg/noAmd.png "amd")
这种方式确定很明显：**首先会发送多个请求，其次引入的js文件顺序不能搞错，否则会报错!**

#### 2.使用require.js
**RequireJS是一个工具库，主要用于客户端的模块管理。**
它的模块管理遵守AMD规范，RequireJS的基本思想是，通过define方法，将代码定义为模块；
通过require方法，实现代码的模块加载。接下来介绍AMD规范在浏览器实现的步骤：
①下载require.js, 并引入
[官网: ](http://www.requirejs.cn/)
[github : ](https://github.com/requirejs/requirejs)
然后将require.js导入项目: `js/libs/require.js`
②创建项目结构
```js
|-js
|-libs
    |-require.js
|-modules
    |-alerter.js
    |-dataService.js
|-main.js
|-index.html
```
③定义require.js的模块代码
```js
// dataService.js文件
// 定义没有依赖的模块
define(function() {
let msg = 'www.baidu.com'
function getMsg() {
    return msg.toUpperCase()
}
return { getMsg } // 暴露模块
})
//alerter.js文件
// 定义有依赖的模块
define(['dataService'], function(dataService) {
let name = 'Tom'
function showMsg() {
    alert(dataService.getMsg() + ', ' + name)
}
// 暴露模块
return { showMsg }
})
// main.js文件
(function() {
require.config({
    baseUrl: 'js/', //基本路径 出发点在根目录下
    paths: {
    //映射: 模块标识名: 路径
    alerter: './modules/alerter', //此处不能写成alerter.js,会报错
    dataService: './modules/dataService'
    }
})
require(['alerter'], function(alerter) {
    alerter.showMsg()
})
})()
```
```html
// index.html文件
<!DOCTYPE html>
<html>
<head>
    <title>Modular Demo</title>
</head>
<body>
    <!-- 引入require.js并指定js主文件的入口 -->
    <script data-main="js/main" src="js/libs/require.js"></script>
</body>
</html>
```
④页面引入require.js模块:
在index.html引入 `<script data-main="js/main" src="js/libs/require.js"></script>`
**此外在项目中如何引入第三方库？**只需在上面代码的基础稍作修改：
```js
// alerter.js文件
define(['dataService', 'jquery'], function(dataService, $) {
let name = 'Tom'
function showMsg() {
    alert(dataService.getMsg() + ', ' + name)
}
$('body').css('background', 'green')
// 暴露模块
return { showMsg }
})
// main.js文件
(function() {
require.config({
    baseUrl: 'js/', //基本路径 出发点在根目录下
    paths: {
    //自定义模块
    alerter: './modules/alerter', //此处不能写成alerter.js,会报错
    dataService: './modules/dataService',
    // 第三方库模块
    jquery: './libs/jquery-1.10.1' //注意：写成jQuery会报错
    }
})
require(['alerter'], function(alerter) {
    alerter.showMsg()
})
})()
```
上例是在alerter.js文件中引入jQuery第三方库，main.js文件也要有相应的路径配置。
小结：通过两者的比较，可以得出AMD模块定义的方法非常清晰，不会污染全局环境，能够清楚地显示依赖关系。
AMD模式可以用于浏览器环境，并且允许非同步加载模块，也可以根据需要动态加载模块。
