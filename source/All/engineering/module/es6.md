---
title: ES6模块化
date: 2021-04-04 14:17:20
tag: js
toc: true
---

ES6模块化的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。
CommonJS和AMD模块，都只能在运行是确定这些东西。比如，CommonJS模块就是对象，输入时必须查找对象属性。
### (1)ES6模块化语法
**export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。**
```js
//定义模块 math.js
let basicNum = 0;
let add = function (a, b) {
    return a + b;
}
export {basicNum, add};
//引用模块
import {basicNum, add} from './math';
function test(ele) {
    ele.textContent = add(99 + basicNum);
}
```
如上所示，使用Import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。
为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定输出。
```js
//export-default.js
export default function () {
    console.log('666');
}
//import-default.js
import customName from './export-default';
customName();//666
```
模块默认输出, 其他模块加载该模块时，import命令可以为该匿名函数指定任意名字。
### (2)ES6模块与CommonJS模块的差异
它们有两个重大差异：
1. **CommonJS模块输出的是一个值的拷贝，ES6模块输出的是值的引用**。
2. **CommonJS模块是运行时加载，ES6模块是编译时输出接口**。
第二个差异是因为CommonJS加载的是一个对象(即module.exports属性)，该对象只有在脚本运行完才会生成。
而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。
下面重点解释第一个差异，我们还是举上面哪个CommonJS模块的加载机制例子：
```js
//lib.js
export let counter = 3;
export function incCounter() {
    counter++;
}
//main.js
import { counter, incCounter } from './lib';
console.log(counter);//3
incCounter();
console.log(counter);//4
```
ES6 模块的运行机制与 CommonJS 不一样。ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块。
### (3)ES6-Babel-Browserify使用教程
简单来说就一句话：使用Babel将ES6编译为ES5代码，使用Browserify编译打包js。
①定义package.json文件
```js
 {
   "name" : "es6-babel-browserify",
   "version" : "1.0.0"
 }
```
②安装`babel-cli, babel-preset-es2015和browserify`
`npm install babel-cli browserify -g`
`npm install babel-preset-es2015 --save-dev`
preset 预设(将es6转换成es5的所有插件打包)
③定义.babelrc文件
```js
  {
    "presets": ["es2015"]
  }
```
④定义模块代码
```js
//module1.js文件
// 分别暴露
export function foo() {
  console.log('foo() module1')
}
export function bar() {
  console.log('bar() module1')
}
//module2.js文件
// 统一暴露
function fun1() {
  console.log('fun1() module2')
}
function fun2() {
  console.log('fun2() module2')
}
export { fun1, fun2 }
//module3.js文件
// 默认暴露 可以暴露任意数据类项，暴露什么数据，接收到就是什么数据
export default () => {
  console.log('默认暴露')
}
// app.js文件
import { foo, bar } from './module1'
import { fun1, fun2 } from './module2'
import module3 from './module3'
foo()
bar()
fun1()
fun2()
module3()
```
⑤ 编译并在index.html中引入
使用Babel将ES6编译为ES5代码(但包含CommonJS语法) : `babel js/src -d js/lib`
使用Browserify编译js : `browserify js/lib/app.js -o js/lib/bundle.js`
然后在index.html文件中引入
```html
<script type="text/javascript" src="js/lib/bundle.js"></script>
```
最后得到如下结果：
 **此外第三方库(以jQuery为例)如何引入呢？**
首先安装依赖`npm install jquery@1`
然后在app.js文件中引入
```js
//app.js文件
import { foo, bar } from './module1'
import { fun1, fun2 } from './module2'
import module3 from './module3'
import $ from 'jquery'

foo()
bar()
fun1()
fun2()
module3()
$('body').css('background', 'green')
```