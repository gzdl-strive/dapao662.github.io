---
title: 项目初始化
date: 2022-03-21 17:49:24
toc: true
---

>**Vite兼容性注意**
Vite需要Node.js版本 >= 12.0.0

### 使用yarn构建项目
```bash
yarn create vite xxx --template vue-ts
```
这里`xxx`换成项目名称
项目结构如下所示:
```
├── public              静态资源
├── src
│   ├── assets           资源
│   ├── components       组件
│   ├── App.vue          主应用
│   ├── env.d.ts         全局声明
│   └── main.ts          主入口
├── .gitignore           git忽略配置
├── index.html           模板文件
├── package.json        依赖包/运行脚本配置文件
├── README.md
├── tsconfig.json        ts配置文件
├── tsconfig.node.json   ts配置文件
└── vite.config.ts       vite配置
```
接着我们输入`cd xxx` => `yarn` => `yarn dev`
项目就可以启动了

### 修改vite.config.ts配置文件
vite.config.ts是vite的核心文件，有关vite的配置都在这里配置。

```ts
import { defineConfig } from 'vite';
const path = require('path');

export default defineConfig({
  plugins: [vue()],
  plublicDir: 'public', //静态资源服务的文件夹
  base: './', //基础路径
  clearScreen: false,//设置为false 可以避免Vite清屏而错过在终端中打印某些关键信息
  server: {
    port: 8080, //端口号
    open: true, //项目启动自动打开,
  },
  resolve: {
    //配置路径别名
    alias: {
      '@': path.resolve(__dirname, './src'),
    }
  },
  //构建选项
  build: {
    outDir: 'dist',//输出目录
    sourcemap: false,//构建后不生成source map文件
    // 将js和css、静态资源拆分到各自的文件夹下
    rollupOptions: {
      output: {
        chunkFileNames: "static/js/[name]-[hash].js",
        entryFileNames: "static/js/[name]-[hash].js",
        assetFileNames: "static/[ext]/[name]-[hash].[ext]",
      }
    },
    minify: 'terser',//terser构建后文件体积更小
    //生产环境移除console
    terserOptions: {//传递给terser的更多minify选项
      compress: {
        drop_console: true,//移除console
        drop_debugger: true,//移除debugger
      }
    },
    brotliSize: false,//启用/禁用 brotli 压缩大小报告。压缩大型输出文件可能会很慢，因此禁用该功能可能会提高大型项目的构建性能
    chunkSizeWarningLimit: 500, //chunk大小警告限制
  }
})
```
上面的配置别名时报错了，使用import方法引入path也不行，使用require引入path提示我们需要安装`@types/node`
**解决方法**
>1、安装types/node依赖
```bash
yarn add @types/node -D
```

>2、在tsconfig.json中添加配置"types": ["node"]
```json
{
  "compilerOptions": {
    //...
    "types": ["node"]
  }
}
```
如上配置，即可使用别名了