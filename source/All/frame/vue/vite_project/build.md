---
title: 打包构建
date: 2022-03-23 14:30:24
toc: true
---

**通过修改package.json中的打包命令来实现区分开发环境和生产环境**

```json
{
  "script": {
    "build:dev": "vue-tsc --noEmit && vite build --mode dev",
    "build:pro": "vue-tsc --noEmit && vite build --mode pro"
  }
}
```

**在项目中就可以通过`import.meta.env`获取到对应的环境**

## 问题1、打包后，放置在assets文件夹中的图片引用路径出错，未能正确指向
```css
/* 源代码 */
.xxxx {
  background: url('@/assets/bg.jpg');
}
```
打包后发现对应的css中路径未能正确指向
**尝试1：可能是路径别名问题-》使用绝对路径->未解决**
**尝试2：将图片放到public文件夹，直接指向，还是不能正确指向**
但是从第二次尝试中发现问题所在，因为vite默认的资源目录就是public文件夹，所以在将图片放到public下后直接`/bg.jpg`即可，但是打包后的路径却变成了`./bg.jpg`. 可以知道是我们vite配置的问题，当时配置了base(开发或生产环境服务的公共基础路径),但是设置为了`base: './'`所以出错了，然后将此配置删除，将之前的代码和图片路径恢复成最开始的情况，打包ok.

## 问题二、打包后的不能兼容Chrome55版本
解决方法：下载vite插件`yarn add @vitejs/plugin-legacy -D`;通过该插件来进行兼容性处理
```ts
//vite.config.ts
import legacy from '@vitejs/plugin-legacy';

export default defineConfig({
  plugins: [
    //...
    legacy({
      targets: ['> 1%',' Chrome >= 55', 'ie >= 11'], //需要兼容的目标列表，可以设置多个
      additionalLegacyPolyfills: ['regenerator-runtime/runtime'], //面向IE11时需要此插件
    })
  ]
})
```
ok

## 问题三、打包时如果存在之前的dist目录，就会报错(待验证，后来又正常了😂)
**修改vite配置的构建选项**
```ts
build: {
  emptyOutDir: false,
}
```

## 问题四、Element-Plus浏览器兼容性问题
![1](/assets/vueImg/elementplus1.png "兼容性")
但是我们需要兼容到谷歌49版本

一开始没有发现该问题(使用el-input、el-aside等组件没有发现异常)。直到使用了表格、下拉框、级联等组件时发现控制台报错`ResizeObserver is undefined`,通过查看官网才得知，`ResizeObserver`是有兼容性要求的[Resize Observer](https://caniuse.com/resizeobserver "Resize Observer兼容性"),可以得知Resize Observer在谷歌中最低需要64版本。

所以我们需要通过polyfill来帮助我们支持更低版本的要求。

**解决**
- 安装resize-observer-polyfill
```bash
npm install resize-observer-polyfill --save-dev
```

- 在main.ts中引入
```ts
import ResizeObserver from 'resize-observer-polyfill';
window.ResizeObserver = ResizeObserver;
```

之前试过之间在main.ts中`import 'resize-observer-polyfill';`，发现还是没有解决问题，还是报undefine,所以我才将它挂载到window下，还好，生效了。