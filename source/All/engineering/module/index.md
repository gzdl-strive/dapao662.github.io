---
title: 模块化
date: 2021-04-04 14:17:20
tag: js
toc: true
---

## 一、模块化的理解
>[模块化的理解](/All/engineering/module/understand "模块化理解")

## 二、模块化的规范
>[CommomJS](/All/engineering/module/cjs "CommonJS")
>[AMD](/All/engineering/module/amd "AMD")
>[CMD](/All/engineering/module/cmd "CMD")
>[ES6模块化](/All/engineering/module/es6 "ES6")

## 三、总结
>* CommonJS规范主要用于服务端编程，加载模块是同步的，这并不适合在浏览器环境，因为同步意味着阻塞加载，浏览器资源是异步加载的，因此有了AMD,CMD解决方案
>* AMD规范在浏览器环境中异步加载模块，而且可以并行加载多个模块。不过，AMD规范开发成本高，代码的阅读和书写比较困难，模块定义方式的语义不顺畅。
>* CMD规范与AMD规范很相似，都用于浏览器编程，依赖就近，延迟执行，可以很容易在Node.js中运行。不过，依赖SPM打包，模块的加载逻辑偏重。
>* ES6在语言标准的层面上，实现了模块功能，而且实现的相当简单，完全可以取代CommonJS和AMD规范，成为浏览器和服务器通用的模块解决方案。