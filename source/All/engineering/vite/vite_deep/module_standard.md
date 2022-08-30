---
title: 模块标准：为什么ESM是模块化的未来
date: 2022-08-30 15:25:24
toc: true
---

## 无模块标准阶段

### 文件划分
文件划分方式是最原始的模块化实现，**简单来说就是将应用的状态和逻辑分散到不同的文件中，然后通过 HTML 中的 script 来一一引入。**

**缺陷：**
  - 模块变量相当于全局声明和定义，会有变量命名冲突问题。
  - 由于变量都在全局定义，我们很难知道某个变量到底属于哪些模块，因此也给调试带来了困难
  - 无法清晰地管理模块之间的依赖关系和加载顺序。假如module-a依赖module-b， script 执行顺序需要手动调整正确，不然可能会产生运行时错误。


### 命名空间
命名空间是模块化的另一种实现手段，它可以解决上述文件划分方式中全局变量定义所带来的一系列问题。

```js
// module-a.js
window.moduleA = {
  data: "moduleA",
  method: function () {
    console.log("execute A's method");
  },
};

// module-b.js
window.moduleB = {
  data: "moduleB",
  method: function () {
    console.log("execute B's method");
  },
};

<script src="./module-a.js"></script>
<script src="./module-b.js"></script>
<script>
  // 此时 window 上已经绑定了 moduleA 和 moduleB
  console.log(moduleA.data);
  moduleB.method();
</script>
```
这样一来，每个变量都有自己专属的命名空间，我们可以清楚地知道某个变量到底属于哪个模块，同时也避免全局变量命名的问题。

### IIEF(私有作用域)
不过，相比于命名空间的模块化手段，IIFE实现的模块化安全性要更高，对于模块作用域的区分更加彻底。
```js
// module-a.js
(function () {
  let data = "moduleA";

  function method() {
    console.log(data + "execute");
  }

  window.moduleA = {
    method: method,
  };
})();

// module-b.js
(function () {
  let data = "moduleB";

  function method() {
    console.log(data + "execute");
  }

  window.moduleB = {
    method: method,
  };
})();
```
我们知道，**每个IIFE 即立即执行函数都会创建一个私有的作用域，在私有作用域中的变量外界是无法访问的，只有模块内部的方法才能访问。**

对于其中的 data变量，我们只能在模块内部的 method 函数中通过闭包访问，而在其它模块中无法直接访问。这就是模块私有成员功能，避免模块私有成员被其他模块非法篡改，相比于命名空间的实现方式更加安全。

但实际上，无论是命名空间还是IIFE，都是为了解决全局变量所带来的命名冲突及作用域不明确的问题，也就是在文件划分方式中所总结的问题 1 和问题 2，而**并没有真正解决另外一个问题——模块加载**。如果模块间存在依赖关系，那么 script 标签的加载顺序就需要受到严格的控制，一旦顺序不对，则很有可能产生运行时 Bug。

## CommonJS规范
CommonJS 是业界最早正式提出的 JavaScript 模块规范，主要用于服务端，随着 Node.js 越来越普及，这个规范也被业界广泛应用。对于模块规范而言，一般会包含 2 方面内容:
- 统一的模块化代码规范
- 实现自动加载模块的加载器(也称loader)

```js
// module-a.js
var data = "hello world";
function getData() {
  return data;
}
module.exports = {
  getData,
};

// index.js
const { getData } = require("./module-a.js");
console.log(getData());
```
代码中使用 require 来导入一个模块，用module.exports来导出一个模块。实际上 Node.js 内部会有相应的 loader 转译模块代码，最后模块代码会被处理成下面这样:
```js
(function (exports, require, module, __filename, __dirname) {
  // 执行模块代码
  // 返回 exports 对象
});
```
对 CommonJS 而言，一方面它定义了一套完整的模块化代码规范，另一方面 Node.js 为之实现了自动加载模块的loader，看上去是一个很不错的模块规范，但也存在一些问题:
模块加载器由 Node.js 提供，依赖了 Node.js 本身的功能实现，比如文件系统，如果 CommonJS 模块直接放到浏览器中是无法执行的。当然, 业界也产生了 browserify 这种打包工具来支持打包 CommonJS 模块，从而顺利在浏览器中执行，相当于社区实现了一个第三方的 loader。
CommonJS 本身约定以同步的方式进行模块加载，这种加载机制放在服务端是没问题的，一来模块都在本地，不需要进行网络 IO，二来只有服务启动时才会加载模块，而服务通常启动后会一直运行，所以对服务的性能并没有太大的影响。但如果这种加载机制放到浏览器端，会带来明显的性能问题。它会产生大量同步的模块请求，浏览器要等待响应返回后才能继续解析模块。也就是说，模块请求会造成浏览器 JS 解析过程的阻塞，导致页面加载速度缓慢。
总之，CommonJS 是一个不太适合在浏览器中运行的模块规范。因此，业界也设计出了全新的规范来作为浏览器端的模块标准，最知名的要数 AMD 了。

## AMD规范
AMD全称为`Asynchronous Module Definition`，即异步模块定义规范。模块根据这个规范，在浏览器环境中会被异步加载，而不会像 CommonJS 规范进行同步加载，也就不会产生同步请求导致的浏览器解析过程阻塞的问题了。我们先来看看这个模块规范是如何来使用的:

```js
// main.js
define(["./print"], function (printModule) {
  printModule.print("main");
});

// print.js
define(function () {
  return {
    print: function (msg) {
      console.log("print " + msg);
    },
  };
});
```

在 AMD 规范当中，我们可以通过 define 去定义或加载一个模块，比如上面的 main 模块和print模块，如果模块需要导出一些成员需要通过在定义模块的函数中 return 出去(参考 print 模块)，如果当前模块依赖了一些其它的模块则可以通过 define 的第一个参数来声明依赖(参考main模块)，这样模块的代码执行之前浏览器会先加载依赖模块。
当然，你也可以使用 require 关键字来加载一个模块，如:

```js
// module-a.js
require(["./print.js"], function (printModule) {
  printModule.print("module-a");
});
```
不过 require 与 define 的区别在于前者只能加载模块，而不能定义一个模块.

由于没有得到浏览器的原生支持，AMD 规范需要由第三方的 loader 来实现，最经典的就是 requireJS 库了，它完整实现了 AMD 规范，至今仍然有不少项目在使用。

不过 AMD 规范使用起来稍显复杂，代码阅读和书写都比较困难。

同期出现的规范当中也有 CMD 规范，这个规范是由淘宝出品的SeaJS实现的，解决的问题和 AMD 一样。不过随着社区的不断发展，SeaJS 已经被requireJS兼容了。

当然，你可能也听说过 UMD (Universal Module Definition)规范，其实它并不算一个新的规范，只是兼容 AMD 和 CommonJS 的一个模块化方案，可以同时运行在浏览器和 Node.js 环境。顺便提一句，后面将要介绍的 ES Module 也具备这种跨平台的能力。

## ES6 Module规范
ES6 Module 也被称作 ES Module(或 ESM)， 是由 ECMAScript 官方提出的模块化规范，作为一个官方提出的规范，ES Module 已经得到了现代浏览器的内置支持。在现代浏览器中，如果在 HTML 中加入含有type="module"属性的 script 标签，那么浏览器会按照 ES Module 规范来进行依赖加载和模块解析，这也是 Vite 在开发阶段实现 no-bundle 的原因，由于模块加载的任务交给了浏览器，即使不打包也可以顺利运行模块代码

```js
// main.js
import { methodA } from "./module-a.js";
methodA();

//module-a.js
const methodA = () => {
  console.log("a");
};

export { methodA };
```

```html
<script type="module" src="./main.js"></script>
````

如果在 Node.js 环境中，你可以在package.json中声明type: "module"属性:
```json
// package.json
{
  "type": "module"
}
```