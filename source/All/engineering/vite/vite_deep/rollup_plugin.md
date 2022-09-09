---
title: 深入理解Rollup插件机制
date: 2022-09-09 09:22:24
toc: true
---

Rollup 设计出了一套完整的插件机制，将自身的核心逻辑与插件逻辑分离，让你能按需引入插件功能，提高了 Rollup 自身的可扩展性。

Rollup 的打包过程中，会定义一套完整的构建生命周期，从开始打包到产物输出，中途会经历一些标志性的阶段，并且在不同阶段会自动执行对应的插件钩子函数(Hook)。对 Rollup 插件来讲，最重要的部分是钩子函数，一方面它定义了插件的执行逻辑，也就是"做什么"；另一方面也声明了插件的作用阶段，即"什么时候做"，这与 Rollup 本身的构建生命周期息息相关。

## Rollup整体构建阶段
在执行rollup命令后，在cli内部的主要逻辑简化如下：
```ts
// Build阶段
const bundle = await rollup.rollup(inputOptions);
// Output阶段
await Promise.all(outputOptions.map(bundle.write));
// 构建结束
await bundle.close();
```
首先，**Build阶段主要负责创建模块依赖图，初始化各个模块的AST以及模块之间的依赖关系**。经过Build阶段的bundle对象其实并没有进行模块的打包，这个对象的作用在于存储各个模块的内容及依赖关系，同时暴露generate和write方法，以进入到后续的output阶段(write和generate方法唯一的区别在于前者打包完产物会写入磁盘，后者不会)。
所以**真正打包的过程会在Output阶段进行**，即在bundle对象的generate或write方法中进行。
因此，对于一次完整的构建过程而言， Rollup 会先进入到 Build 阶段，解析各模块的内容及依赖关系，然后进入Output阶段，完成打包及输出的过程。对于不同的阶段，Rollup 插件会有不同的插件工作流程。

## 拆解插件工作流
### 插件Hook类型
#### 1、根据构建阶段分类
- Build Hook
>	在Build阶段执行的钩子函数，在这个阶段主要进行模块代码的转换、AST解析以及模块依赖的解析，那么这个阶段的Hook对于代码的操作粒度一般为模块级别，也就是单文件级别。

- Output Hook
>	Output Hook(官方称为`Output Generation Hook`)，主要进行代码的打包，对于代码而言，操作力度一般为chunk级别(chunk 通常指很多文件打包到一起的产物)

#### 2、根据执行方式分类
- Async 和 Sync
>Async和Sync钩子函数，两者其实是相对的，分别代表异步和同步的钩子函数，两者最大的区别在于同步钩子里面不能有异步逻辑，而异步钩子可以有。

- Parallel
>	这里指并行的钩子函数。如果有多个插件实现了这个钩子的逻辑，一旦有钩子函数是异步逻辑，则并发执行钩子函数，不会等待当前钩子完成(底层使用 Promise.all)。
比如对于Build阶段的buildStart钩子，它的执行时机其实是在构建刚开始的时候，各个插件可以在这个钩子当中做一些状态的初始化操作，但其实插件之间的操作并不是相互依赖的，也就是可以并发执行，从而提升构建性能。反之，对于需要依赖其他插件处理结果的情况就不适合用 Parallel 钩子了，比如 transform。

- Squential
>Sequential 指串行的钩子函数。这种 Hook 往往适用于插件间处理结果相互依赖的情况，前一个插件 Hook 的返回值作为后续插件的入参，这种情况就需要等待前一个插件执行完 Hook，获得其执行结果，然后才能进行下一个插件相应 Hook 的调用，如transform。

- First
>如果有多个插件实现了这个 Hook，那么 Hook 将依次运行，直到返回一个非 null 或非 undefined 的值为止。比较典型的 Hook 是 resolveId，一旦有插件的 resolveId 返回了一个路径，将停止执行后续插件的 resolveId 逻辑。
实际上不同的类型是可以叠加的，Async/Sync 可以搭配后面三种类型中的任意一种，比如一个 Hook既可以是 Async 也可以是 First 类型

### Build阶段工作流
![build](/assets/engineeringImg/viteImg/rollupPlugin1.webp "Build阶段")
1、首先经过options钩子进行配置的转换，得到处理后的配置对象。
2、随之Rollup会调用buildStart钩子，正式开启构建流程。
3、Rollup先进入到resolveId钩子中解析文件路径。(从input配置指定的入口文件开始)
4、Rollup通过调用load钩子加载模块内容
5、紧接着Rollup执行所有的transform钩子来对模块内容进行自定义的转换，比如babel转译。
6、现在 Rollup 拿到最后的模块内容，进行 AST 分析，得到所有的 import 内容，调用 moduleParsed 钩子:
  6.1 如果是普通的 import，则执行 resolveId 钩子，继续回到步骤3
  6.2 如果是动态 import，则执行 resolveDynamicImport 钩子解析路径，如果解析成功，则回到步骤4加载模块，否则回到步骤3通过 resolveId 解析路径。
7、直到所有的 import 都解析完毕，Rollup 执行buildEnd钩子，Build 阶段结束。

当然，在 Rollup 解析路径的时候，即执行resolveId或者resolveDynamicImport的时候，有些路径可能会被标记为external(翻译为排除)，也就是说不参加 Rollup 打包过程，这个时候就不会进行load、transform等等后续的处理了。

在流程图最上面，不知道大家有没有注意到watchChange和closeWatcher这两个 Hook，这里其实是对应了 rollup 的watch模式。当你使用 rollup --watch 指令或者在配置文件配有watch: true的属性时，代表开启了 Rollup 的watch打包模式，这个时候 Rollup 内部会初始化一个 watcher 对象，当文件内容发生变化时，watcher 对象会自动触发watchChange钩子执行并对项目进行重新构建。在当前打包过程结束时，Rollup 会自动清除 watcher 对象调用closeWacher钩子。


### Output阶段工作流
![output](/assets/engineeringImg/viteImg/rollupPlugin2.webp "Output阶段")

1、执行所有插件的outputOptions钩子函数，对output配置进行转换。
2、执行renderStart,并发执行renderStart钩子，正式开始打包。
3、并发执行所有插件的banner、footer、intro、outro钩子(底层调用Promise.all包裹所有的这四种钩子函数)，这四个钩子功能很简单，就是往打包产物的固定位置(比如头部和尾部)插入一些自定义的内容，比如协议声明内容、项目介绍等等。
4、从入口模块开始扫描，针对动态 import 语句执行 renderDynamicImport钩子，来自定义动态 import 的内容。
5、对每个即将生成的 chunk，执行 augmentChunkHash钩子，来决定是否更改 chunk 的哈希值，在 watch 模式下即可能会多次打包的场景下，这个钩子会比较适用。
6、如果没有遇到 import.meta 语句，则进入下一步，否则：
  6.1 对于 import.meta.url 语句调用 resolveFileUrl 来自定义 url 解析逻辑
  6.2 对于其他import.meta 属性，则调用 resolveImportMeta 来进行自定义的解析。
7、接着 Rollup 会生成所有 chunk 的内容，针对每个 chunk 会依次调用插件的renderChunk方法进行自定义操作，也就是说，在这里时候你可以直接操作打包产物了。
8、随后会调用 generateBundle 钩子，这个钩子的入参里面会包含所有的打包产物信息，包括 chunk (打包后的代码)、asset(最终的静态资源文件)。你可以在这里删除一些 chunk 或者 asset，最终这些内容将不会作为产物输出。
9、前面提到了rollup.rollup方法会返回一个bundle对象，这个对象是包含generate和write两个方法，两个方法唯一的区别在于后者会将代码写入到磁盘中，同时会触发writeBundle钩子，传入所有的打包产物信息，包括 chunk 和 asset，和 generateBundle钩子非常相似。不过值得注意的是，这个钩子执行的时候，产物已经输出了，而 generateBundle 执行的时候产物还并没有输出
10、当上述的bundle的close方法被调用时，会触发closeBundle钩子，到这里 Output 阶段正式结束
>注意: 当打包过程中任何阶段出现错误，会触发 renderError 钩子，然后执行closeBundle钩子结束打包。