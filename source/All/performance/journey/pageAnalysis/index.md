---
title: 第四站：页面解析与处理
date: 2021-05-25 16:24:20
tag: performance
toc: true
---

[🔙上一站- 服务端响应](/All/performance/journey/response "服务端响应")

在上一站中，我们简单介绍了服务端处理与响应，到目前为止我们已经经历了很多环节，也已经有了许多「性能优化的武器」。像是
* 利用各级缓存进行优化
* 提前进行 DNS 查询或建立连接等方式加速请求
* 在服务端避免不必要的耗时
* ……
不过，不要掉以轻心，后续仍然有大量的工作等待我们来优化。下面就到了客户端接收响应的阶段了。

## 主要工作
这一阶段浏览器需要处理的东西很多，为了更好地理解性能优化，我们主要将其分成几个部分：
* 页面DOM的解析；
* 页面静态资源的加载，包括了页面引用的JavaScript/CSS/图片/字体等;
* 静态资源的解析与处理，像是JavaScript的执行、CSSOM的构建与样式合成等；

## 1、注意资源在页面文档中的位置
我们的目标是收到内容就尽快解析处理，页面有依赖的资源就尽快发送请求，收到响应则尽快处理。然而，这个美好的目标也有可能会被我们不小心破坏。

JavaScript脚本和CSS样式表在关于DOM元素的属性，尤其是样式属性上都有操作的权力。这就像是一个多线程问题。服务端多线程编程中经常通过锁来保证线程间的互斥。回到咱们的前端，现在也是两方在竞争同一个资源，显然也是会有互斥的问题。这就带来了DOM解析、JavaScript加载与执行、CSS加载与使用之间的一些互斥关系。

仅仅看DOM与CSS的关系，则如下图所示：
![domCSS](/assets/performanceImg/domCSS.png "domCSS")
**HTML解析为DOM Tree,CSS解析为CSSOM，两者再合成Render Tree,并行执行，非常完美** 

然而，当JavaScript入场之后，局面就变了:
![domCSSJS](/assets/performanceImg/domCSSJS.png "domCSSJS")
根据标准规范，在JavaScript中可以访问DOM。因此当遇到JavaScript后会阻塞DOM的解析。与此同时，为避免CSS与JavaScript之间的竞态，CSSOM的构建会阻塞JavaScript的脚本执行。总结起来就是----
>JavaScript会阻塞DOM的构建，而CSSOM的构建又会阻塞JavaScript的执行。

所以这就是为什么在优化的最佳实践中，我们基本都推荐把 CSS 样式表放在 `<head> `之中（即页面的头部），把 JavaScript 脚本放在 `<body>` 的最后（即页面的尾部）。

## 2、使用defer和async
上面提到了，当DOM解析遇到JavaScript脚本时，会停止解析，开始下载脚本并执行，在恢复解析，相当于阻塞了DOM构建.
那除了将脚本放在body的最后，还有什么优化方法吗？是有的。
可以使用`defer`或`async`属性。两者都会防止JavaScript脚本的下载阻塞DOM构建。但是两者也有区别，最直观的表现如下：
![deferAsync](/assets/performanceImg/deferAsync.jpeg "deferAsync")
`defer`会在HTML解析完成后，安装脚本出现的次序再顺序执行；而async则是下载完成就立即开始执行，同时阻塞页面解析，不保证脚本间的执行顺序。
根据它们的特点，推荐在一些与主业务无关的 JavaScript 脚本上使用 async。例如统计脚本、监控脚本、广告脚本等。这些脚本一般都是一份独立的文件，没有外部依赖，不需要访问 DOM，也不需要有严格的执行时机限制。在这些脚本上使用 async 可以有效避免这些非核心功能的加载影响页面解析速度.

## 3、页面文档压缩
HTML 的文档大小也会极大影响响应体下载的时间。一般会进行 HTML 内容压缩（uglify）的同时，使用文本压缩算法（例如 gzip）进行文本的压缩。关于资源压缩这一块，在下一节的内容中还会再详细进行介绍。

>[下一站：页面静态资源🔜](/All/performance/journey/staticResources "页面静态资源")