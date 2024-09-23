---
title: 依赖预构建
date: 2022-09-01 10:42:24
toc: true
---

大家都知道， Vite 是一个提倡 `no-bundle` 的构建工具，相比于传统的 Webpack，能做到开发时的模块按需编译，而不用先打包完再加载。

模块代码：**源代码（业务代码）和第三方依赖代码(node_modules中的代码)** ==> 所谓no-boundle只是对于源代码而言，对于第三方依赖代码，Vite还是选择bundle，并且使用速度极快的打包器esbuild来完成这一过程。

## 为什么需要预构建
为什么在开发阶段我们要对第三方依赖进行预构建？如果不进行预构建会怎么样？
首先 Vite 是基于浏览器原生 ES 模块规范实现的 Dev Server，不论是应用代码，还是第三方依赖的代码，理应符合 ESM 规范才能够正常运行。

但可惜，我们没有办法控制第三方的打包规范。就目前来看，还有相当多的第三方库仍然没有 ES 版本的产物，比如大名鼎鼎的 react:

```ts
// react 入口文件
// 只有 CommonJS 格式

if (process.env.NODE_ENV === "production") {
  module.exports = require("./cjs/react.production.min.js");
} else {
  module.exports = require("./cjs/react.development.js");
}
```

这种 *CommonJS 格式的代码在 Vite 当中无法直接运行，我们需要将它转换成 ESM 格式的产物。*

此外，还有一个比较重要的问题——请求瀑布流问题。比如说，知名的loadsh-es库本身是有 ES 版本产物的，可以在 Vite 中直接运行。但实际上，它在加载时会发出特别多的请求，导致页面加载的前几秒几都乎处于卡顿状态.
我在应用代码中调用了debounce方法，这个方法会依赖很多工具函数 ==> 每个import都会触发一次新的文件请求，因此在这种依赖层级深、涉及模块数量多的情况下，会触发成百上千个网络请求，巨大的请求量加上 Chrome 对同一个域名下只能同时支持 6 个 HTTP 并发请求的限制，导致页面加载十分缓慢，与 Vite 主导性能优势的初衷背道而驰。不过，在进行依赖的预构建之后，lodash-es这个库的代码被打包成了一个文件，这样请求的数量会骤然减少，页面加载也快了许多。

总之，依赖预构建主要做了两件事情：
- 一是将其他格式(如 UMD 和 CommonJS)的产物转换为 ESM 格式，使其在浏览器通过 `<script type="module"><script>`的方式正常加载。
- 二是打包第三方库的代码，将各个第三方库分散的文件合并到一起，减少 HTTP 请求数量，避免页面加载性能劣化。

而这两件事情全部由性能优异的 **Esbuild** (基于 Golang 开发)完成，而不是传统的 `Webpack/Rollup`，所以也不会有明显的打包性能问题，反而是 Vite 项目启动飞快(秒级启动)的一个核心原因。

## 如何开启预构建

### 自动开启
当我们在第一次启动项目的时候，可以在命令行窗口看见`Pre-bundling dependencies:......`,同时，在项目启动成功后，你可以在根目录下的`node_modules`中发现**.vite目录**，这就是预构建产物文件存放的目录 ----在浏览器访问页面后，打开 `Dev Tools` 中的网络调试面板，你可以发现第三方包的引入路径已经被重写:
```ts
import React from "react";
// 路径被重写，定向到预构建产物文件中
import __vite__cjsImport0_react from "/node_modules/.vite/react.js?v=979739df";
const React = __vite__cjsImport0_react.__esModule
  ? __vite__cjsImport0_react.default
  : __vite__cjsImport0_react;
```

当然，除了 HTTP 缓存，Vite 还设置了本地文件系统的缓存，所有的预构建产物默认缓存在`node_modules/.vite`目录中。如果以下 3 个地方都没有改动，Vite 将一直使用缓存文件:
- `package.json`的**dependencies字段**
- 各种包管理器的`lock`文件
- **optimizeDeps配置内容**

### 手动开启
上面提到了预构建中本地文件系统的产物缓存机制，而少数场景下我们不希望用本地的缓存文件，比如需要调试某个包的预构建结果，我推荐使用下面任意一种方法清除缓存，还有手动开启预构建:
- 删除`node_modules/.vite`目录。
- 在 Vite 配置文件中，将**server.force设为true**。
- 命令行执行`npx vite --force`或者`npx vite optimize。`

>Vite 项目的启动可以分为两步，第一步是依赖预构建，第二步才是 Dev Server 的启动，`npx vite optimize`相比于其它的方案，仅仅完成第一步的功能。

## 自定义配置详解

### 入口文件entries
第一个是参数是`optimizeDeps.entries`，通过这个参数你可以自定义预构建的入口文件。
实际上，在项目第一次启动时，Vite 会默认抓取项目中所有的 HTML 文件（如当前脚手架项目中的index.html），将 HTML 文件作为应用入口，然后根据入口文件扫描出项目中用到的第三方依赖，最后对这些依赖逐个进行编译。

那么，当默认扫描 HTML 文件的行为无法满足需求的时候，比如项目入口为vue格式文件时，你可以通过 entries 参数来配置:
```ts
//vite.config.ts
{
	optimizeDeps: {
		//为一个字符串数组
		entries: ["./src/main.vue"]
	}
}
```

当然，entries 配置也支持 glob 语法，非常灵活，如:
```ts
// 将所有的 .vue 文件作为扫描入口
entries: ["**/*.vue"];
```

不光是.vue文件，Vite 同时还支持各种格式的入口，包括: `html、svelte、astro、js、jsx、ts和tsx`。可以看到，只要可能存在import语句的地方，Vite 都可以解析，并通过内置的扫描机制搜集到项目中用到的依赖，通用性很强。

### 添加一些依赖includes
除了 entries，include 也是一个很常用的配置，它决定了可以强制预构建的依赖项，使用方式很简单:
```ts
// vite.config.ts
optimizeDeps: {
  // 配置为一个字符串数组，将 `lodash-es` 和 `vue`两个包强制进行预构建
  include: ["lodash-es", "vue"];
}
```
它在使用上并不难，真正难的地方在于，如何找到合适它的使用场景。前文中我们提到，**Vite 会根据应用入口(entries)自动搜集依赖，然后进行预构建**，这是不是说明 Vite 可以百分百准确地搜集到所有的依赖呢？事实上并不是，**某些情况下 Vite 默认的扫描行为并不完全可靠**，这就需要联合配置include来达到完美的预构建效果了。接下来，我们好好梳理一下到底有哪些需要配置include的场景。

#### 场景一、动态Import
在某些动态 import 的场景下，由于 Vite 天然按需加载的特性，经常会导致某些依赖只能在运行时被识别出来。
```ts
// src/locales/zh_CN.js
import objectAssign from "object-assign";
console.log(objectAssign);

// main.tsx
const importModule = (m) => import(`./locales/${m}.ts`);
importModule("zh_CN");
```
在这个例子中，动态 import 的路径只有运行时才能确定，无法在预构建阶段被扫描出来。
![pre-bundle](/assets/engineeringImg/viteImg/prebundle1.webp "动态import")

这段 log 的意思是: Vite 运行时发现了新的依赖，随之重新进行依赖预构建，并刷新页面。这个过程也叫二次预构建。在一些比较复杂的项目中，这个过程会执行很多次，如下面的日志信息所示:

```java
[vite] new dependencies found: @material-ui/icons/Dehaze, @material-ui/core/Box, @material-ui/core/Checkbox, updating...
[vite] ✨ dependencies updated, reloading page...
[vite] new dependencies found: @material-ui/core/Dialog, @material-ui/core/DialogActions, updating...
[vite] ✨ dependencies updated, reloading page...
[vite] new dependencies found: @material-ui/core/Accordion, @material-ui/core/AccordionSummary, updating...
[vite] ✨ dependencies updated, reloading page...
```
然而，二次预构建的成本也比较大。我们不仅需要把预构建的流程重新运行一遍，还得重新刷新页面，并且需要重新请求所有的模块。尤其是在大型项目中，这个过程会严重拖慢应用的加载速度！因此，我们要尽力避免运行时的二次预构建。
具体怎么做呢？你可以通过include参数提前声明需要按需加载的依赖:
```ts
//vite.config.ts
{
	optimizeDeps: {
		include: [
			// 按需加载的依赖都可以声明到这个数组里
			'object-assign'
		]
	}
}
```

#### 场景二、某些包被手动exclude
exclude 是`optimizeDeps`中的另一个配置项，与include相对，用于将某些依赖从预构建的过程中排除。不过这个配置并不常用，也不推荐大家使用。如果真遇到了要在预构建中排除某个包的情况，需要注意它所依赖的包是否具有 ESM 格式，如下面这个例子:

```ts
// vite.config.ts
{
  optimizeDeps: {
    exclude: ["@loadable/component"];
  }
}
```

可以看到浏览器控制台会出现如下的报错 -> 这是为什么呢? 我们刚刚手动 exclude 的包`@loadable/component`本身具有 ESM 格式的产物，但它的某个依赖`hoist-non-react-statics`的产物并没有提供 ESM 格式，导致运行时加载失败。
这个时候include配置就派上用场了，我们可以强制对`hoist-non-react-statics`这个间接依赖进行预构建:
```ts
// vite.config.ts
{
  optimizeDeps: {
    include: [
      // 间接依赖的声明语法，通过`>`分开, 如`a > b`表示 a 中依赖的 b
      "@loadable/component > hoist-non-react-statics",
    ];
  }
}
```
在include参数中，我们将所有不具备 ESM 格式产物包都声明一遍，这样再次启动项目就没有问题了。

### 自定义Esbuild行为
Vite 提供了`esbuildOptions` 参数来让我们自定义 Esbuild 本身的配置，常用的场景是加入一些 Esbuild 插件:
```ts
// vite.config.ts
{
  optimizeDeps: {
    esbuildOptions: {
       plugins: [
        // 加入 Esbuild 插件
      ];
    }
  }
}
```
这个配置主要是处理一些特殊情况，如某个第三方包本身的代码出现问题了。

#### 特殊情况：第三方包出现问题怎么办？
由于我们无法保证第三方包的代码质量，在某些情况下我们会遇到莫名的第三方库报错。我举一个常见的案例——`react-virtualized`库。这个库被许多组件库用到，但它的 ESM 格式产物有明显的问题，在 Vite 进行预构建的时候会直接抛出错误

![prebundle2](/assets/engineeringImg/viteImg/prebundle2.webp "第三方包出现问题")

##### 改第三方库代码

首先，我们能想到的思路是直接修改第三方库的代码，不过这会带来团队协作的问题，你的改动需要同步到团队所有成员，比较麻烦。

好在，我们可以使用`patch-package`这个库来解决这类问题。一方面，它能记录第三方库代码的改动，另一方面也能将改动同步到团队每个成员。
`patch-package` 官方只支持 npm 和 yarn，而不支持 pnpm，不过社区中已经提供了支持pnpm的版本，这里我们来安装一下相应的包:

`pnpm i @milahu/patch-package -D`
》注意: 要改动的包在 `package.json` 中必须声明确定的版本，**不能有~或者^的前缀**。

接着，我们进入第三方库的代码中进行修改，**先删掉无用的 import 语句**，再在命令行输入: `npx patch-package react-virtualized`

现在根目录会多出`patches`目录记录第三方包内容的更改，随后我们在`package.json的scripts`中增加如下内容：
```json
{
  "scripts": {
    // 省略其它 script
    "postinstall": "patch-package"
  }
}
```
这样一来，每次安装依赖的时候都会通过 `postinstall` 脚本自动应用 patches 的修改，解决了团队协作的问题。

##### 加入Esbuild插件
通过 Esbuild 插件修改指定模块的内容，这里我给大家展示一下新增的配置内容:
```ts
// vite.config.ts
const esbuildPatchPlugin = {
  name: "react-virtualized-patch",
  setup(build) {
    build.onLoad(
      {
        filter:
          /react-virtualized\/dist\/es\/WindowScroller\/utils\/onScroll.js$/,
      },
      async (args) => {
        const text = await fs.promises.readFile(args.path, "utf8");

        return {
          contents: text.replace(
            'import { bpfrpt_proptype_WindowScroller } from "../WindowScroller.js";',
            ""
          ),
        };
      }
    );
  },
};

// 插件加入 Vite 预构建配置
{
  optimizeDeps: {
    esbuildOptions: {
      plugins: [esbuildPatchPlugin];
    }
  }
}
```