---
title: 静态资源：开发阶段模块加载与生产环境优化
date: 2022-08-31 14:18:02
toc: true
---

## 图片加载
>图片是项目中最常用的静态资源之一，本身包括了非常多的格式，诸如 `png、jpeg、webp、avif、gif`，当然，也包括经常用作图标的 **svg 格式**。

### 使用场景
>1、HTML/JSX中，通过Img标签来加载图片
2、CSS通过background属性加载图片
3、JS通过脚本动态指定图片的src属性

### Vite中使用
配置别名，方便引入：
```js
// vite.config.ts
import path from 'path';

{
  resolve: {
    // 别名配置
    alias: {
      '@assets': path.join(__dirname, 'src/assets')
    }
  }
}
```
这样 Vite 在遇到@assets路径的时候，会自动帮我们定位至根目录下的src/assets目录。值得注意的是，alias 别名配置不仅在 JavaScript 的 import 语句中生效，在 CSS 代码的 @import 和 url导入语句中也同样生效。

```js
// Header/index.tsx
import React, { useEffect } from 'react';
import { devDependencies } from '../../../package.json';
import styles from './index.module.scss';
// 1. 导入图片
import logoSrc from '@assets/imgs/vite.png';

// 方式一
export function Header() {
  return (
    <div className={`p-20px text-center ${styles.header}`}>
      <!-- 省略前面的组件内容 -->
      <!-- 使用图片 -->
      <img className="m-auto mb-4" src={logoSrc} alt="" />
    </div>
  );
}

// 方式二
export function Header() {
  useEffect(() => {
    const img = document.getElementById('logo') as HTMLImageElement;
    img.src = logoSrc;
  }, []);
  return (
    <div className={`p-20px text-center ${styles.header}`}>
      <!-- 省略前面的组件内容 -->
      <!-- 使用图片 -->
      <img id="logo" className="m-auto mb-4" alt="" />
    </div>
  );
}
```

## JSON加载
Vite 中已经内置了对于 JSON 文件的解析，底层使用`@rollup/pluginutils` 的 `dataToEsm` 方法将 `JSON` 对象转换为一个包含各种具名导出的 ES 模块：

```ts
import { version } from '../../../package.json';

// 不过你也可以在配置文件禁用按名导入的方式:
// vite.config.ts
{
  json: {
    stringify: true
  }
}
```

这样会将 JSON 的内容解析为`export default JSON.parse("xxx")`，这样会失去按名导出的能力，不过在 JSON 数据量比较大的时候，可以优化解析性能。

## Web Worker、WebAssembly
//....

## 其他静态资源
除了上述的一些资源格式，Vite 也对下面几类格式提供了内置的支持:
媒体类文件，包括`mp4、webm、ogg、mp3、wav、flac和aac`。
字体类文件。包括`woff、woff2、eot、ttf` 和 `otf`。
文本类。包括`webmanifest、pdf和txt`。
也就是说，你可以在 Vite 将这些类型的文件当做一个 ES 模块来导入使用。如果你的项目中还存在其它格式的静态资源，你可以通过`assetsInclude`配置让 Vite 来支持加载:
```ts
// vite.config.ts
{
  assetsInclude: ['.gltf']
}
```

## 特殊资源后缀
Vite 中引入静态资源时，也支持在路径最后加上一些特殊的 query 后缀，包括:
- `?url`: 表示获取资源的路径，这在只想获取文件路径而不是内容的场景将会很有用。
- `?raw`: 表示获取资源的字符串内容，如果你只想拿到资源的原始内容，可以使用这个后缀。
- `?inline`: 表示资源强制内联，而不是打包成单独的文件。

## 生产环境处理
在生产环境下，我们面临着一些问题：
  - 部署域名怎么配置？
  - 资源打包成单文件还是作为 Base64 格式内联?
  - 图片太大了怎么压缩？
  - svg 请求数量太多了怎么优化？

### 1、自定义部署域名
一般在我们访问线上的站点时，站点里面一些静态资源的地址都包含了相应域名的前缀，如:
```html
<img src="https://xxx.com/logo.png" />
```
以上面这个地址例子，`https://xxx.com`是 **CDN 地址前缀**，`/logo.png`则是我们开发阶段使用的路径。
那么，我们是不是需要在上线前把图片先上传到 CDN，然后将代码中的地址手动替换成线上地址呢？这样就太麻烦了！

在 Vite 中我们可以有更加自动化的方式来实现地址的替换，只需要在配置文件中指定base参数即可:
```ts
// vite.config.ts
// 是否为生产环境，在生产环境一般会注入 NODE_ENV 这个环境变量，见下面的环境变量文件配置
const isProduction = process.env.NODE_ENV === 'production';
// 填入项目的 CDN 域名地址
const CDN_URL = 'xxxxxx';

// 具体配置
{
  base: isProduction ? CDN_URL: '/'
}

// .env.development
NODE_ENV=development

// .env.production
NODE_ENV=production
```

注意在项目根目录新增的两个环境变量文件`.env.development和.env.production`，顾名思义，即分别在开发环境和生产环境注入一些环境变量，这里为了区分不同环境我们加上了NODE_ENV，你也可以根据需要添加别的环境变量。

打包的时候 Vite 会自动将这些环境变量替换为相应的字符串。

### 2、单文件OR内联
在 Vite 中，所有的静态资源都有两种构建方式，一种是打包成一个**单文件**，另一种是通过 `base64 编码`的格式**内嵌到代码**中。

这两种方案到底应该如何来选择呢？

>对于比较小的资源，适合内联到代码中，一方面对代码体积的影响很小，另一方面可以减少不必要的网络请求，优化网络性能。而对于比较大的资源，就推荐单独打包成一个文件，而不是内联了，否则可能导致上 MB 的 base64 字符串内嵌到代码中，导致代码体积瞬间庞大，页面加载性能直线下降。

Vite 中内置的优化方案是下面这样的:
如果静态资源体积 `>= 4KB`，则提取成单独的文件
如果静态资源体积 `< 4KB`，则作为 base64 格式的字符串内联
上述的4 KB即为提取成单文件的临界值，当然，这个临界值你可以通过`build.assetsInlineLimit`自行配置，如下代码所示:

```ts
// vite.config.ts
{
  build: {
    // 8 KB
    assetsInlineLimit: 8 * 1024
  }
}
```
svg 格式的文件不受这个临时值的影响，始终会打包成单独的文件，因为它和普通格式的图片不一样，需要动态设置一些属性

### 3、图片压缩
在 JavaScript 领域有一个非常知名的图片压缩库`imagemin`，作为一个底层的压缩工具，前端的项目中经常基于它来进行图片压缩，比如 Webpack 中大名鼎鼎的`image-webpack-loader`。社区当中也已经有了开箱即用的 Vite 插件——`vite-plugin-imagemin`：

```bash
pnpm i vite-plugin-imagemin -D
```

随后在 Vite 配置文件中引入:
```ts
//vite.config.ts
import viteImagemin from 'vite-plugin-imagemin';

{
  plugins: [
    // 忽略前面的插件
    viteImagemin({
      // 无损压缩配置，无损压缩下图片质量不会变差
      optipng: {
        optimizationLevel: 7
      },
      // 有损压缩配置，有损压缩下图片质量可能会变差
      pngquant: {
        quality: [0.8, 0.9],
      },
      // svg 优化
      svgo: {
        plugins: [
          {
            name: 'removeViewBox'
          },
          {
            name: 'removeEmptyAttrs',
            active: false
          }
        ]
      }
    })
  ]
}
```

### 4、雪碧图优化
在实际的项目中我们还会经常用到各种各样的 svg 图标，虽然 svg 文件一般体积不大，但 Vite 中对于 svg 文件会始终打包成单文件，大量的图标引入之后会导致网络请求增加，大量的 HTTP 请求会导致网络解析耗时变长，页面加载性能直接受到影响。这个问题怎么解决呢？

HTTP2 的多路复用设计可以解决大量 HTTP 的请求导致的网络加载性能问题，因此雪碧图技术在 HTTP2 并没有明显的优化效果，这个技术更适合在传统的 HTTP 1.1 场景下使用(比如本地的 Dev Server)。

比如在 Header 中分别引入 5 个 svg 文件:
```ts
import Logo1 from '@assets/icons/logo-1.svg';
import Logo2 from '@assets/icons/logo-2.svg';
import Logo3 from '@assets/icons/logo-3.svg';
import Logo4 from '@assets/icons/logo-4.svg';
import Logo5 from '@assets/icons/logo-5.svg';
```

这里顺便说一句，Vite 中提供了`import.meta.glob`的语法糖来解决这种批量导入的问题，如上述的 import 语句可以写成下面这样:
`const icons = import.meta.glob('../../assets/icons/logo-*.svg');`
对象的 value 都是动态 import，适合按需加载的场景。在这里我们只需要同步加载即可，可以使用 `import.meta.globEager`来完成:
`const icons = import.meta.globEager('../../assets/icons/logo-*.svg');`
```tsx
// Header/index.tsx
const iconUrls = Object.values(icons).map(mod => mod.default);

// 组件返回内容添加如下
{iconUrls.map((item) => (
  <img src={item} key={item} width="50" alt="" />
))}
```
回到页面中，我们发现浏览器分别发出了 5 个 svg 的请求
假设页面有 100 个 svg 图标，将会多出 100 个 HTTP 请求，依此类推。我们能不能把这些 svg 合并到一起，从而大幅减少网络请求呢？
答案是可以的。这种合并图标的方案也叫雪碧图，我们可以通过vite-plugin-svg-icons来实现这个方案，首先安装一下这个插件:
```ts
pnpm i vite-plugin-svg-icons -D
```
接着在 Vite 配置文件中增加如下内容:
```ts
// vite.config.ts
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons';

{
  plugins: [
    // 省略其它插件
    createSvgIconsPlugin({
      iconDirs: [path.join(__dirname, 'src/assets/icons')]
    })
  ]
}
```
在 src/components目录下新建SvgIcon组件:
```tsx
// SvgIcon/index.tsx
export interface SvgIconProps {
  name?: string;
  prefix: string;
  color: string;
  [key: string]: string;
}

export default function SvgIcon({
  name,
  prefix = 'icon',
  color = '#333',
  ...props
}: SvgIconProps) {
  const symbolId = `#${prefix}-${name}`;

  return (
    <svg {...props} aria-hidden="true">
      <use href={symbolId} fill={color} />
    </svg>
  );
}
```
现在我们回到 Header 组件中，稍作修改:
```ts
// index.tsx
const icons = import.meta.globEager('../../assets/icons/logo-*.svg');
const iconUrls = Object.values(icons).map((mod) => {
  // 如 ../../assets/icons/logo-1.svg -> logo-1
  const fileName = mod.default.split('/').pop();
  const [svgName] = fileName.split('.');
  return svgName;
});

// 渲染 svg 组件
{iconUrls.map((item) => (
  <SvgIcon name={item} key={item} width="50" height="50" />
))}
```
最后在src/main.tsx文件中添加一行代码:
```ts
import 'virtual:svg-icons-register';
```
现在回到浏览器的页面中，发现雪碧图已经生成
雪碧图包含了所有图标的具体内容，而对于页面每个具体的图标，则通过 use 属性来引用雪碧图的对应内容
如此一来，我们就能将所有的 svg 内容都内联到 HTML 中，省去了大量 svg 的网络请求。