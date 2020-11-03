---
title: webpack初体验
date: 2020-11-03 09:28:16
tag: webpack
---

## 目录
    webpack
      build
      node_modules
      src
       index.js
       index.css
       data.json
### index.js--入口文件内容
```js
import data from './data.json';
//import './index.css';
console.log(data);
function add (x, y) {
  return x + y;
}
console.log(add(2, 3));
```

## 运行指令
### 开发环境
```js
webpack ./src/index.js -o ./build/built.js --mode=development
//webpack会以./src/index.js为入口文件开始打包,打包输出到./build/built.js--整体打包环境是开发环境
```
### 生产环境
```js
webpack ./src/index.js -o ./build/built.js --mode=production
//webpack会以 ./src/index.js为入口文件开始打包，打包后输出到 ./build/built.js整体打包环境，是生产环境
```

## 结论：
>1. `webpack`能处理**js/json**,不能处理**css/img**等其他资源
2. 生产环境和开发环境将ES6模块化编译成浏览器能识别的模块化
3. 生产环境比开发环境多一个压缩js代码
