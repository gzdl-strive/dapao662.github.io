---
title: 代码分离
date: 2021-12-22 10:23:02
toc: true
---

代码分离是 webpack 中最引人注目的特性之一。此特性能够把代码分离到不同的 bundle 中，然后可以按需加载或并行加载这些文件。代码分离可以用于获取更小的 bundle，以及控制资源加载优先级，如果使用合理，会极大影响加载时间。

常见的代码分离方法有三种：

- **入口起点**: 使用 entry 配置手动地分离代码
- **防止重复**: 使用 Entry dependencies 或者 SplitChunksPlugin 去重和分离 chunk.
- **动态导入**: 通过模块的内联函数调用来分离代码

### 入口起点

这是迄今为止最简单直观的分离代码的方式。不过，这种方式手动配置较多，并有一些隐患，我们将会解决这些问题。先来看看如何从 main bundle 中分离 another module(另一个模块)：

在 src 目录下创建 `another-module.js` 文件：

```js
// src/another-module.js
import _ from "lodash";

console.log(_.join(["Another", "module", "loaded!"], ""));
```

这个模块依赖了 lodash ，需要安装一下：

```powershell
$ npm install lodash --save-dev
```

修改配置文件：

```js
module.exports = {
  entry: {
    index: "./src/index.js",
    another: "./src/another-module.js",
  },
  output: {
    filename: "[name].bundle.js",
  },
};
```

执行编译`npx webpack`
![codeSplit](/assets/engineeringImg/webpackImg/codeSplit1.png "入口分离")

查看 app.html ：

```html
<!-- ... -->
<body>
  <script defer src="index.bundle.js"></script>
  <script defer src="another.bundle.js"></script>
</body>
<!-- ... -->
```

两个入口的 bundle 文件都被链接到了 app.html 中
我们再来修改一下 index.js 文件:

```js
import _ from "lodash";
console.log(_.join(["index", "module", "loaded!"], " "));
```

执行编译，我们发现： `index.bundle.js` 文件大小也骤然增大了，可以 `lodash.js` 也被打包到了 `index.bundle.js` 中了。
正如前面提到的，这种方式的确存在一些隐患：

- 如果入口 chunk 之前包含一些重复的模块，那些重复模块都会被引入到各个 bundle 中。
- 这种方法不够灵活，并且不能动态地将核心应用程序逻辑中的代码拆分出来。

以上两点中，第一点对我们的实例来说无疑是个问题，因为之前我们在./src/index.js 中也引入过 lodash,这样就在两个 bundle 中造成重复引用.

### 防止重复

- 入口依赖
  配置`dependOn option`选项，这样可以在多个 chunk 之间共享模块:

```js
module.exports = {
  entry: {
    index: {
      import: "./src/index.js",
      dependOn: "shared",
    },
    another: {
      import: "./src/another-module.js",
      dependOn: "shared",
    },
    shared: "lodash",
  },
};
```

执行编译后，可以看到`index.bundle.js` 与 `another.bundle.js` 共享的模块 `lodash.js` 被打包到一个单独的文件 `shared.bundle.js` 中。

- SplitChunksPlugin
  `SplitChunksPlugin`插件可以将公共的依赖模块提取到已有的入口 chunk 中，或者提取到一个新生成的 chunk。让我们使用这个插件，将之前的示例中重复的`lodash`模块去除.

```js
module.exports = {
  entry: {
    index: "./src/index.js",
    another: "./src/another-module.js",
  },
  optimization: {
    splitChunks: {
      chunks: "all",
    },
  },
};
```

使用 `optimization.splitChunks` 配置选项之后，现在应该可以看出，`index.bundle.js` 和 `another.bundle.js` 中已经移除了重复的依赖模块。需要注意的是，插件将 lodash 分离到单独的 chunk，并且将其从 main bundle 中移除，减轻了大小。

### 动态导入

当涉及到动态代码拆分时，webpack 提供了两个类似的技术。第一种，也是推荐选择的方式是，使用符合 ECMAScript 提案 的 `import()` 语法 来实现动态导入。第二种，则是 webpack 的遗留功能，使用 webpack 特定的 require.ensure 。让我们先尝试使用第一种……
创建 `async-module.js` 文件：

```js
//async-module.js
function getComponent() {
  return import("lodash")
    .then(({ default: _ }) => {
      const element = document.createElement("div");
      element.innerHTML = _.join(["Hello", "World"], "-");
      return element;
    })
    .catch((error) => "An error occurred while loading the component");
}

getComponent().then((component) => {
  document.body.appendChild(component);
});
```

在入口文件中导入：

```js
import "./async-module";
```

执行编译，静态和动态载入的模块都正常工作了

### 懒加载

懒加载或者按需加载，是一种很好的优化网页或应用的方式。这种方式实际上是先把你的代码在一些逻辑断点处分离开，然后在一些代码块中完成某些操作后，立即引用或即将引用另外一些新的代码块。这样加快了应用的初始加载速度，减轻了它的总体体积，因为某些代码块可能永远不会被加载。
创建一个 math.js 文件，在主页面中通过点击按钮调用其中的函数：

```js
//math.js
export const add = () => { return x + y };
export const minus = () => { return x - y };
```

编辑index.js文件
```js
const button = document.createElement('button'); 
button.textContent = '点击执行加法运算'; 
button.addEventListener('click', () => { 
  import(/* webpackChunkName: 'math' */ './math.js')
    .then(({ add }) => { 
      console.log(add(4, 5)); }) 
    })
document.body.appendChild(button);
```
>这里有句注释，我们把它称为 webpack 魔法注释： `webpackChunkName: 'math'` ,告诉webpack打包生成的文件名为 math 。

启动webpack-dev-server服务，第一次加载完页面， math.bundle.js 不会加载，当点击按钮后，才加载math.bundle.js 文件。

### 预获取/预加载模块

Webpack v4.6.0+ 增加了对预获取和预加载的支持。
在声明 import 时，使用下面这些内置指令，可以让 webpack 输出 "resource hint(资源提示)"，来告知浏览器：

- **prefetch**(预获取)：将来某些导航下可能需要的资源
- **preload**(预加载)：当前导航下可能需要资源


#### 预获取(prefetch)

下面这个 prefetch 的简单示例中，编辑 index.js 文件：
```js
const button = document.createElement('button');
button.textContent = '点击执行加法运算';
button.addEventListener('click', () => { 
  import(/* webpackChunkName: 'math', webpackPrefetch: true */ './math.js')
    .then(({ add }) => { 
      console.log(add(4, 5)) 
    }) })
document.body.appendChild(button);
```

添加第二句魔法注释： `webpackPrefetch: true`

告诉 webpack 执行预获取。这会生成 `<link rel="prefetch" href="math.js">`,并追加到页面头部，指示着浏览器在闲置时间预取 math.js 文件

启动dev-server服务，我们发现，在还没有点击按钮时， `math.bundle.js` 就已经下载下来了。同时，在app.html 里webpack自动添加了一句:
![prefecth](/assets/engineeringImg/webpackImg/prefetch.png "预加载")；

#### 预加载(preload)

与 prefetch 指令相比，preload 指令有许多不同之处：
- preload chunk 会在父 chunk 加载时，以并行方式开始加载。prefetch chunk会在父 chunk 加载结束后开始加载。
- preload chunk 具有中等优先级，并立即下载。prefetch chunk 在浏览器闲置时下载。
- preload chunk 会在父 chunk 中立即请求，用于当下时刻。prefetch chunk 会用于未来的某个时刻。
- 浏览器支持程度不同。


创建一个 print.js 文件：
```js
export const print = () => {
  console.log('preload chunk.');
}
```

修改index.js文件
```js
const button2 = document.createElement('button'); 
button2.textContent = '点击执行字符串打印';
button2.addEventListener('click', () => { 
  import(/* webpackChunkName: 'print', webpackPreload: true */ './print.js')
    .then(({ print }) => { 
      print(4, 5) }) 
    })
document.body.appendChild(button2);
```
启动服务，仔细观察，发现 print.bundle.js 未被下载，因为我们配置的是webpackPreload , 是在父 chunk 加载时，以并行方式开始加载。点击按钮才加载的模块不会事先加载的。
