---
title: 提高开发效率，完善开发规范
date: 2021-12-22 15:05:24
toc: true
---

## 1、source-map

作为一个开发工程师——无论是什么开发，要求开发环境最不可少的一点功能就是——debug 功能。 之前我们通过 webpack, 将我们的源码打包成了 bundle.js 。 试想：实际上客户端(浏览器)读取的是打包后的 bundle.js ,那么当浏览器执行代码报错的时候，报错的信息自然也是 bundle 的内容。 我们如何将报错信息(bundle 错误的语句及其所在行列)映射到源码上？

是的，souce-map。

webpack 已经内置了 sourcemap 的功能,我们只需要通过简单的配置，将可以开启它.

```js
module.exports = {
  //开启source map
  //开发中推荐使用source map,生产环境一般不开启
  devtool: "source-map",
};
```

当我们执行打包命令之后，我们发现 bundle 的最后一行总是会多出一个注释，指向打包出的`bundle.map.js`(sourcemap 文件)。 sourcemap 文件用来描述 源码文件和 bundle 文件的代码位置映射关系。基于它，我们将 bundle 文件的错误信息映射到源码文件上。

除开`'source-map'`外，还可以基于我们的需求设置其他值，`webpack——devtool`一共提供了 7 种 SourceMap 模式

|          name           |                                                      age                                                       |
| :---------------------: | :------------------------------------------------------------------------------------------------------------: |
|          eval           |                  每个 module 会封装到 eval 里包裹起来执行，并且会在末尾追加注释//# sourceURL                   |
|       source-map        |                                            生成一个 SourceMap 文件                                             |
|    hidden-source-map    |                                和 source-map 一样，但不会在 bundle 末尾追加注释                                |
|    inline-source-map    |                                 在 bundle 的末尾追加 DataUrl(base64)形式的注释                                 |
|     eval-source-map     |      每个 module 会被封装到 eval 里包裹起来执行，并且会在末尾追加一个 DataUrl 的注释//# sourceMappingURL       |
|    cheap-source-map     | 生成一个没有列信息（column-mappings）的 SourceMaps 文件，不包含 loader 的 sourcemap（譬如 babel 的 sourcemap） |
| cheap-module-source-map |  生成一个没有列信息（column-mappings）的 SourceMaps 文件，同时 loader 的 sourcemap 也被简化为只包含对应行的。  |

要注意的是，生产环境我们一般不会开启 sourcemap 功能，主要有两点原因:

> 1、通过 bundle 和 sourcemap 文件，可以反编译出源码--也就是说，线上产物有 sourcemap 文件的话，就意味着有暴露源码的风险
> 2、我们可以观察到，sourcemap 文件的体积相对比较巨大，这跟我们生产环境的追求不同(生产环境追求更小更轻量的 bundle)

一道思考题: 有时候我们期望能第一时间通过线上的错误信息，来追踪到源码位置，从而快速解决掉 bug 以减轻损失。但又不希望 sourcemap 文件报漏在生产环境，有比较好的方案吗？

## 2、devServer

开发环境下，我们往往需要启动一个 web 服务，方便我们模拟一个用户从浏览器中访问我们的 web 服务，读取我们的打包产物，以观测我们的代码在客户端的表现。webpack 内置了这样的功能，我们只需要简单的配置就可以开启它。在此之前，我们需要安装它

```powershell
yarn add -D webpack-dev-server
```

`devServer.proxy`基于强大的中间件 `http-proxy-middleware` 实现的，因此它支持很多的配置项，我们基于此，可以做应对绝大多数开发场景的定制化配置

基础使用:

```js
const path = require("path");
module.exports = {
  //...
  devServer: {
    static: {
      directory: path.join(__dirname, "dist"),
    }, //默认是把/dist目录当作web服务的根目录
    compress: true, //可选择开启gzip压缩功能，对应静态资源请求的响应头里的Content-Encoding: gzip
    port: 3000, //端口号
  },
  //...
};
```

为了方便，我们配置一下工程的脚本命令，在 package.json 的 scripts 里

```json
{
  //...
  "scripts": {
    //...
    "dev": "webpack serve --mode development"
  }
}
```

注意！如果您需要指定配置文件的路径，请在命令的后面添加 --config [path], 比如:

```powershell
webpack serve --mode development --config webpack.config.js
```

这时，当我们`yarn dev`(或者`npm run dev`)时，就可以在日志里看到————它启动了一个 http 服务。 (`webpack-dev-server`的最底层实现是源自于 node 的 http 模块。)

**我们可以根据自己的需求定制化 devServer 的参数对象，比如添加响应头，开启代理来解决跨域问题, http2, https 等功能**

- 添加响应头
  有些场景需求下，我们需要为所有响应添加 headers,来对资源的请求和响应打入标志，以便做一些安全防范，或者方便发生异常后做请求的链路追踪。比如:

```js
//webpack-config
module.exports = {
  //...
  devServer: {
    headers: {
      "x-Fast-Id": "pasdfja5dlhkf",
    },
  },
};
```

这时，在浏览器中右键检查(或者使用 f12 快捷键)，在 Network 一栏查看任意资源访问，我们发现响应头里成功打入了一个 FastId。

**headers 的配置也可以传一个函数：**

```js
module.exports = {
  //...
  devServer: {
    headers: () => {
      return { "X-Bar": ["key1=value1", "key2=value2"] };
    },
  },
};
```

比如我们的标志 ID(X-Fast-Id)，很明显这个 id 不应该写死，而是随机生成的————这时候你就可以用函数实现这个功能。

- 开启代理
  我们打包出的 js bundle 里有时会含有一些对特定接口的网络请求(ajax/fetch).要注意，此时客户端地址是在 `http://localhost:3000/` 下，假设我们的接口来自`http://localhost:4001/` ，那么毫无疑问，此时控制台里会报错并提示你跨域。如何解决这个问题？ 在开发环境下，我们可以使用 devServer 自带的 proxy 功能：

```js
module.exports = {
  //...
  devServer: { proxy: { "/api": "http://localhost:4001" } },
};
```

现在，对 `/api/users` 的请求会将请求代理到 `http://localhost:4001/api/users` 。 如果不希望传递/api，则需要重写路径：

```js
module.exports = {
  //...
  devServer: {
    proxy: {
      "/api": {
        target: "http://localhost:4001",
        pathRewrite: { "^/api": "" },
      },
    },
  },
};
```

默认情况下，将不接受在 HTTPS 上运行且证书无效的后端服务器。 如果需要，可以这样修改配置：

```js
module.exports = {
  //...
  devServer: {
    proxy: {
      "/api": { target: "https://other-server.example.com", secure: false },
    },
  },
};
```

- https
  如果想让我们的本地 http 服务变成 https 服务，我们只需要这样配置

```js
module.exports = {
  //...
  devServer: {
    https: true,
  },
};
```

注意，此时我们访问`http://localhost:port` 是无法访问我们的服务的，我们需要在地址栏里加前缀：`https`: 注意:由于默认配置使用的是自签名证书，所以有得浏览器会告诉你是不安全的，但我们依然可以继续访问它。 当然我们也可以提供自己的证书——如果有的话:

```js
module.exports = {
  devServer: {
    https: {
      cacert: "./server.pem",
      pfx: "./server.pfx",
      key: "./server.key",
      cert: "./server.crt",
      passphrase: "webpack-dev-server",
      requestCert: true,
    },
  },
};
```

- http2
  如果想要配置 http2，那么直接设置：

```js
module.exports = {
  devServer: { http2: true },
};
```

即可，http2 默认自带 https 自签名证书，当然我们仍然可以通过 https 配置项来使用自己的证书。

- historyApiFallback
  如果我们的应用是个 SPA(单页面应用)，当路由到/some 时(可以直接在地址栏里输入)，会发现此时刷新页面后，控制台会报错。
  `GET http://localhost:3000/some 404 (Not Found)`
  此时打开 network，刷新并查看，就会发现问题所在———浏览器把这个路由当作了静态资源地址去请求，然而我们并没有打包出/some 这样的资源，所以这个访问无疑是 404 的。 如何解决它？ 这种时候，我们可以通过配置来提供页面代替任何 404 的静态资源响应：

```js
module.exports = {
  //...
  devServer: {
    historyApiFallback: true,
  },
};
```

此时重启服务刷新后发现请求变成了 index.html。 当然，在多数业务场景下，我们需要根据不同的访问路径定制替代的页面，这种情况下，我们可以使用 rewrites 这个配置项。 类似这样:

```js
module.exports = {
  //...
  devServer: {
    historyApiFallback: {
      rewrites: [
        { from: /^\/$/, to: "/views/landing.html" },
        { from: /^\/subpage/, to: "/views/subpage.html" },
        { from: /./, to: "/views/404.html" },
      ],
    },
  },
};
```

- 开发服务器主机
  如果你在开发环境中起了一个 devserve 服务，并期望你的同事能访问到它，你只需要配置：

```js
module.exports = {
  //...
  devServer: { host: "0.0.0.0" },
};
```

这时候，如果你的同事跟你处在同一局域网下，就可以通过局域网 ip 来访问你的服务啦。

## 3、模块热替换与热加载

### 模块热替换

> 模块热替换(`HMR - hot module replacement`)功能会在应用程序运行过程中，替换、添加或删除 模块，而无需重新加载整个页面

启用 webpack 的 热模块替换 特性，需要配置`devServer.hot`参数:

```js
module.exports = {
  //...
  devServer: {
    hot: true,
  },
};
```

此时我们实现了基本的模块热替换功能

- HMR 加载样式 如果你配置了 style-loader，那么现在已经同样支持样式文件的热替换功能了.

```js
module.exports = {
  module: {
    rules: [{ test: /\.css$/, use: ["style-loader", "css-loader"] }],
  },
};
```

这是因为 style-loader 的实现使用了 module.hot.accept，在 CSS 依赖模块更新之后，会对 style 标签打补丁。从而实现了这个功能。

### 热加载

热加载(文件更新时，自动刷新我们的服务和页面) 新版的 webpack-dev-server 默认已经开启了热加载的功能。 它对应的参数是`devServer.liveReload`，默认为 true。 注意，如果想要关掉它，要将 liveReload 设置为 false 的同时，也要关掉 hot

```js
module.exports = {
  //...
  devServer: {
    liveReload: false, //默认为true，即开启热更新功能。
  },
};
```

## 4、eslint

eslint 是用来扫描我们所写的代码是否符合规范的工具。 往往我们的项目是多人协作开发的，我们期望统一的代码规范，这时候可以让 eslint 来对我们进行约束。 严格意义上来说，eslint 配置跟 webpack 无关，但在工程化开发环境中，它往往是不可或缺的。

```powershell
yarn add eslint -D
```

配置 eslint，只需要在根目录下添加一个`.eslintrc`文件(或者.eslintrc.json, .js 等)。 当然，我们可以使用 eslint 工具来自动生成它

```powershell
npx eslint --init
```

我们可以看到控制台里的展示：

```js
/*
webpack5demo % npx eslint --init 
✔ How would you like to use ESLint? · syntax 
✔ What type of modules does your project use? · esm 
✔ Which framework does your project use? · react 
✔ Does your project use TypeScript? · No / Yes 
✔ Where does your code run? · browser 
✔ What format do you want your config file to be in? · JSON
*/
```

并生成了一个配置文件`.eslintrc.json`，这样我们就完成了 eslint 的基本规则配置。eslint 配置文件里的配置项含义如下：

1. env 指定脚本的运行环境。每种环境都有一组特定的预定义全局变量。此处使用的 browser 预定义了浏览器环境中的全局变量，es6 启用除了 modules 以外的所有 ECMAScript 6 特性（该选项会自动设置 ecmaVersion 解析器选项为 6）
2. globals 脚本在执行期间访问的额外的全局变量。也就是 env 中未预定义，但我们又需要使用的全局变量。
3. extends 检测中使用的预定义的规则集合。
4. rules 启用的规则及其各自的错误级别，会合并 extends 中的同名规则，定义冲突时优先级更高。
5. parserOptions ESLint 允许你指定你想要支持的 JavaScript 语言选项。ecmaFeatures 是个对象，表示你想使用的额外的语言特性，这里 jsx 代表启用 JSX。ecmaVersion 用来指定支持的 ECMAScript 版本 。默认为 5，即仅支持 es5，你可以使用 6、7、8、9 或 10 来指定你想要使用的 ECMAScript 版本。你也可以用使用年份命名的版本号指定为 2015（同 6），2016（同 7），或 2017（同 8）或 2018（同 9）或 2019 (same as 10)。上面的 env 中启用了 es6，自动设置了 ecmaVersion 解析器选项为 6。 plugins plugins 是一个 npm 包，通常输出 eslint 内部未定义的规则实现。rules 和 extends 中定义的规则，并不都在 eslint 内部中有实现。比如 extends 中的 plugin:react/recommended，其中定义了规则开关和等级，但是这些规则如何生效的逻辑是在其对应的插件 ‘react’ 中实现的

接下来，我们在这个配置文件里额外添加一个规则:

```js
{
  //...others
  "rules": {
    "no-console": "warn", //我们在rules里面自定义我们的约束规范
  }
}
```

我们通过命令来让 elisnt 检测代码——在我们的 package.scripts 里添加一个脚本命令:

```js
//package.json
{
  "script": {
    //others
    "eslint": "eslint ./src"
  }
}
```

执行它：`npm run eslint`

### 结合 webpack 使用

我们期望 eslint 能够实时提示报错而不必等待执行命令。 这个功能可以通过给自己的 IDE(代码编辑器)安装对应的 eslint 插件来实现。 然而，不是每个 IDE 都有插件，如果不想使用插件，又想实时提示报错，那么我们可以结合 webpack 的打包编译功能来实现。

```js
module.exports = {
  //...
  {
    test: /\.(js|jsx)$/,
    exclude: /node_modules/,
    use: ['babel-loader', 'eslint-loader']
  }
  //...
}
```

因为我们使用了 devServer，因此需要在 devServer 下添加一个对应的配置参数:

```js
module.exports = {
  //...
  devServer: {
    liveReload: false, //默认为true,即开启热更新功能
  },
};
```

现在我们就可以实时地看到代码里的不规范报错啦。

## 5、git-hooks 与 husky

为了保证团队里的开发人员提交的代码符合规范，我们可以在开发者上传代码时进行校验。 我们常用 husky 来协助进行代码提交时的 eslint 校验。在使用 husky 之前，我们先来研究一下 git-hooks 。

**环境准备**
先在我们的目录下执行 git init，创建本地仓库，添加.`gitignore`文件,内容`**/node_modules`

### git-hooks

我们回到项目的根目录下。运行 ls -a 命令 ———— “-a”可以显示隐藏目录(目录名的第一位是.)。我们可以看到，存在一个".git"名称的文件夹。事实上，在我们项目中根目录下运行 git 命令时，git 会根据它来工作。接来下我们进入到这个文件夹，进一步查看它内部的内容。

```powershell
cd .git
ls -a
```

我们发现它内部还挺有料！不慌，我们这节课仅仅只讲到其中的一个内容 ————hooks，可以看到，当前目录下存在一个 hooks 文件夹，顾名思义，这个文件夹提供了 git 命令相关的钩子.

继续往里看。

```powershell
cd hooks
ls -a
```

ok，那我们可以看到有很多 git 命令相关的文件名。比如"`pre-commit.sample` `pre-push.sample`"。回到正题——我们期望在 git 提交(commit)前，对我们的代码进行检测，如果不能通过检测，就无法提交我们的代码

自然而然的，这个动作的时机应该是？————"pre commit",也就是 commit 之前

现在，我们查看一下 pre-commit.sample 的内容。

```bash
# cat命令可以查看一个文件的内容
cat pre-commit.sample
```

OK，它返回了这样的内容，是一串 shell 注释。翻译过来大概意思是，这是个示例钩子，然后我们看到了这一句话

```bash
# To enable this hook, rename this file to "pre-commit".
```

意思是要启用这个钩子的话，我们就把这个文件的后缀名去掉。

所以,我们直接新建`.git/hooks/pre-commit` 文件
**win10 在 git bash 中添加.git/hooks/pre-commit**

> 在 git commit 时报错`error: cannot spawn .git/hooks/pre-commit: No such file or directory`
> 解决方法：.git/hooks/pre-commit 中需要在头部添加`#!/bin/sh`

虽然这样对我们本地来讲是可行的，但要注意，.git 文件夹的改动无法同步到远端仓库。所以我们期望将 git-hook 的执行权移交到外面来。

好的，我们回到项目的根目录下，然后我们新建一个文件夹，暂时命名为"`.mygithooks`"
然后在此文件夹下，新增一个 git-hook 文件,命名为"pre-commit"，并写入内容：`echo pre-commit执行啦`
好了，我们新建了自己的 git-hook，但此时 git 并不能识别。下面我们执行这行命令:

```bash
# 项目根目录下
git config core.hooksPath .mygithooks
```

上述命令给我们自己的文件，配置了 git-hook 的执行权限。
但这个时候我们 git commit 的话，可能会报这样的 waring，并且没有执行我们的 shell：

```bash
# mac下需要给文件加可执行权限，需要执行
chmod +x .mygithooks/pre-commit
# window下只需要在文件头部添加 #!/bin/sh
```

ok！现在我们尝试执行 git add . && git commit -m "any meesage"。

我们发现控制台日志会先打印 “pre-commit 执行啦”。这意味着成功啦,这时只需要把 pre-commit 的内容修改为`npx eslint ./src`,就会在提交代码之前进行 eslint 校验

**总结**
也就是说，我们搞 git-hook 的话，要分三步走：

1. 新增任意名称文件夹以及文件 pre-commit(这个文件名字比如跟要使用的 git-hook 名字一致)！
2. 执行以下命令来移交 git-hook 的配置权限

```bash
git config core.hooksPath .mygithooks
```

3. 给这个文件添加可执行权限：

```bash
# mac下需要给文件加可执行权限，需要执行
chmod +x .mygithooks/pre-commit
# window下只需要在文件头部添加 #!/bin/sh
```

### husky

husky 在升级到 7.x 后，做了跟我们上述同样的事。
安装它之前，我们需要在 package.json 中的 script 里，先添加

> 1.  `yarn add husky -D`
> 2.  `npx husky install`
> 3. 在 package.json 添加脚本

```json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```
>4. 在.husky目录下创建pre-commit文件，内容和之前一样即可

prepare是一个npm钩子，意思是安装依赖的时候，会先执行husky install命令。

我们安装了7.x的husky会发现，项目根目录下生成了.husky的文件夹。
当然，7.x的husky似乎是有bug的，如果不能正常使用，那么我们只需要验证两件事：

1. 是否移交了git-hook的配置权限？
执行命令 "git config --list"查看core.hooksPath配置是否存在，是否正确指向了.husky。如果没有，我们只需要手动的给加上就行：
```bash
git config core.hooksPath .husky
```

2. 是否是可执行文件？
这时我们的husky就正常了。