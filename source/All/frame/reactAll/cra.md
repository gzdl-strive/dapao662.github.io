---
title: React应用(基于脚手架)
date: 2021-12-10 14:37:24
toc: true
---

## 创建项目

**创建项目并启动**
第一步，全局安装：`npm i -g create-react-app`
第二步，切换到想创项目的目录，使用命令：`create-react-app hello-react`
第三步，进入项目文件夹：`cd hello-react`
第四步，启动项目：`npm start`

**react 脚手架项目结构**

> public ---- 静态资源文件夹
>
> > favicon.icon ------ 网站页签图标
> > index.html -------- 主页面
> > logo192.png ------- logo 图
> > logo512.png ------- logo 图
> > manifest.json ----- 应用加壳的配置文件
> > robots.txt -------- 爬虫协议文件

> src ---- 源码文件夹
>
> > App.css -------- App 组件的样式
> > App.js --------- App 组件
> > App.test.js ---- 用于给 App 做测试
> > index.css ------ 样式
> > index.js ------- 入口文件
> > logo.svg ------- logo 图
> > reportWebVitals.js --- 页面性能分析文件(需要 web-vitals 库的支持)
> > setupTests.js ---- 组件单元测试的文件(需要 jest-dom 库的支持)

## React 脚手架配置代理

### 方法一

> 在 package.json 中追加如下配置

```json
"proxy":"http://localhost:5000"
```

说明：

1. 优点：配置简单，前端请求资源时可以不加任何前缀。
2. 缺点：不能配置多个代理。
3. 工作方式：上述方式配置代理，当请求了 3000 不存在的资源时，那么该请求会转发给 5000 （优先匹配前端资源）

### 方法二

1. 第一步：创建代理配置文件
   在 src 下创建配置文件：`src/setupProxy.js`
2. 编写 setupProxy.js 配置具体代理规则：

```js
const proxy = require("http-proxy-middleware");

module.exports = function (app) {
  app.use(
    proxy("/api1", {
      //api1是需要转发的请求(所有带有/api1前缀的请求都会转发给5000)
      target: "http://localhost:5000", //配置转发目标地址(能返回数据的服务器地址)
      changeOrigin: true, //控制服务器接收到的请求头中host字段的值
      /*
      changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
      changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
      changeOrigin默认值为false，但我们一般将changeOrigin值设为true
      */
      pathRewrite: { "^/api1": "" }, //去除请求前缀，保证交给后台服务器的是正常请求地址(必须配置)
    }),
    proxy("/api2", {
      target: "http://localhost:5001",
      changeOrigin: true,
      pathRewrite: { "^/api2": "" },
    })
  );
};
```
说明：
1. 优点：可以配置多个代理，可以灵活的控制请求是否走代理。
2. 缺点：配置繁琐，前端请求资源时必须加前缀。