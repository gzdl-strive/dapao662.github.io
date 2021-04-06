---
title: CMD
date: 2021-04-04 14:49:20
tag: js
toc: true
---

CMD规范专门用于浏览器端，模块的加载是异步的，模块使用时才会加载执行。
CMD规范整合了CommonJS和AMD规范的特点。在 Sea.js 中，所有 JavaScript 模块都遵循 CMD模块定义规范。
### (1)、CMD规范基本语法
```js
// 定义暴露模块：
    // 定义没有依赖的模块
    define(function(require, exports, module) {
        exports.xxx = value;
        module.exports = value;
    })
    //定义有依赖的模块
    define(function(require, exports, module) {
        //引入依赖模块(同步)
        var module2 = require('./module2');
        //引入依赖模块(异步)
        require.async('./module3', function (m3) {

        })
        //暴露模块
        exports.xxx = value
    })
// 引入使用模块：
define(function (require) {
  var m1 = require('./module1')
  var m4 = require('./module4')
  m1.show()
  m4.show()
})
```

### (2)sea.js简单使用教程
①下载sea.js, 并引入
[官网: ](http://seajs.org/)
[github : ](https://github.com/seajs/seajs)
然后将sea.js导入项目: `js/libs/sea.js`
②创建项目结构
```js
|-js
|-libs
    |-sea.js
|-modules
    |-module1.js
    |-module2.js
    |-module3.js
    |-module4.js
    |-main.js
|-index.html
```
③定义sea.js的模块代码
```js
// module1.js文件
define(function (require, exports, module) {
//内部变量数据
var data = 'atguigu.com'
//内部函数
function show() {
    console.log('module1 show() ' + data)
}
//向外暴露
exports.show = show
})
// module2.js文件
define(function (require, exports, module) {
module.exports = {
    msg: 'I Will Back'
}
})
// module3.js文件
define(function(require, exports, module) {
const API_KEY = 'abc123'
exports.API_KEY = API_KEY
})
// module4.js文件
define(function (require, exports, module) {
//引入依赖模块(同步)
var module2 = require('./module2')
function show() {
    console.log('module4 show() ' + module2.msg)
}
exports.show = show
//引入依赖模块(异步)
require.async('./module3', function (m3) {
    console.log('异步引入依赖模块3  ' + m3.API_KEY)
})
})
// main.js文件
define(function (require) {
var m1 = require('./module1')
var m4 = require('./module4')
m1.show()
m4.show()
})
```
④在index.html中引入
```html
<script type="text/javascript" src="js/libs/sea.js"></script>
<script type="text/javascript">
seajs.use('./js/modules/main')
</script>
```
最后得到结果如下：
![CMD](/assets/jsImg/cmd.png)