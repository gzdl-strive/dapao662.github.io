---
title: 环境变量
date: 2022-08-28 11:22:24
toc: true
---

>环境变量：他的主要作用就是让开发者区分不同的运行环境，来实现 兼容开发和生产

例如 `npm run dev `就是开发环境  `npm run build` 就是生产环境等等

Vite 在一个特殊的 `import.meta.env` 对象上暴露环境变量
```ts
{
  "BASE_URL": "/", // 部署时的URL前缀
  "MODE": "development", // 运行模式
  "DEV": true,  // 是否在dev环境
  "PROD": false, // 是否是build 环境
  "SSR": false //是否是SSR 服务端渲染模式
}
```

### 配置额外的环境变量
>在根目录新建env 文件 可以创建多个,命名方式.env.[name] ===> 例如`.env.development`

1. 根目录新建.env.development文件
```
VITE_CONFIG_MODE = http://env.com
```

2. 修改package.json配置 ==> 后加--mode env文件名称 ==> 例如`.env.development`就加--`--mode development`
```json
{
  "script": {
    "dev": "vite --mode development"
  }
}
```
随后我们就可以在App.vue中输出`import.meta.env`即可

### 在vite.config.js中使用环境配置
```js
import { loadEnv, defineConfig } from 'vite';

export default ({ mode }) => {
  console.log(loadEnv(mode, process.cwd())); //{ VITE_CONFIG_MODE: 'http://env.com' }

  return defineConfig({
    // 你的配置
  })
}
```
我们就可以通过环境变量这个值 做一些事情比如 切换接口url 等 