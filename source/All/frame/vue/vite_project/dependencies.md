---
title: 基础依赖
date: 2022-03-22 09:55:24
toc: true
---

## 1、css预处理器：Scss
```bash
yarn add sass -D
```
直接运行即可，在.vue中也可以直接使用了
`scss`变量如果要导出到全局的话，需要在vite.config.ts中添加如下配置项：
```ts
//...
export default defineConfig({
  //...
  //配置scss全局变量
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "@/style/variables.scss";`,//配置变量
      }
    }
  }
})
```

## 2、Vue-Router和Pinia
```bash
yarn add vue-router pinia -S
```
**另外，pinia还需要用到数据持久化，所以还需要安装持久化插件**
```bash
yarn add pinia-plugin-persist -S
```
在src下新建store和router目录分别用来管理状态和路由

### store
```ts
// store/index.ts
import { defineStore } from 'pinia';
// 1. 定义容器、导出容器
// 参数1：容器的ID，必须是唯一的，后面Pinia会把所有的容器挂载到根容器
// 参数2：一些选项对象，也就是state、getter和action
// 返回值：一个函数，调用即可得到容器实例
const useMainStore = defineStore('main', {
  //类似与Vue2组件中的data，用于存储全局状态数据，但有两个要求
  // 1. 必须是函数，目的是为了在服务端渲染的时候避免交叉请求导致的数据状态污染
  // 2. 必须是箭头函数，这样是为了更好的 TS 类型推导
  state: () => {
    return {
      info: 'pinia使用',
      count: 10
    }
  },
  getters: {
    //类似于组件的computed,用来封装计算数学，具有缓存的功能
    count10(state) {
      return state.count += 10;
    }
  },
  actions: {
    // 类似于vue2组件的methods，用于封装业务逻辑，修改state
    // 注意：不能使用箭头函数来定义actions，因为箭头函数绑定外部的this
    changeState() {
      this.count += 10;
      this.info = 'actions修改数据';
    },
    changeStates(num: number) {
      this.count += num + 2;
      this.info = 'actions修改数据';
    }
  },
  persist: {
    //数据持久化
    enabled: true, //是否开启
    strategies: [
      {
        //自定义名称
        key: 'setting-store',
        storage: localStorage,//存储方式
        paths: ['theme', 'showTagsView'] //包含的state中字段,不写默认是全部
      }
    ]
  }
});

export default useMainStore;
```

**在main.ts中引入pinia**
```ts
import { createPinia } from 'pinia';
import piniaPluginPersist from 'pinia-plugin-persist';

const pinia = createPinia();
pinia.use(piniaPluginPersist);

createApp(App)
  .use(pinia)
  //....
```

### router
```ts
//===== router/routes.ts => 放置路由表
import { RouteRecordRaw } from 'vue-router';

const routes: RouteRecordRaw[] = [
  {
    name: 'login',
    path: '/login',
    component: () => import('@/pages/login'),
    meta: {
      noKeepAlive: true
    }
  },
];

export default routes;
//===== router/index.ts =>整合路由表，路由守卫的一些功能,比如路由扁平化
import { createRouter, RouteLocationNormalized, createWebHistory } from 'vue-router';
import routes from './routes';

const router = createRouter({
  history: createWebHistory(),
  routes: [
    //路由404匹配
    {
      path: "/:pathMatch(.*)*",
      component: () => import("@/pages/Result"),
      name: "Result",
      meta: {
        noKeepAlive: true
      }
    },
    ...routes,
  ],
});
//路由守卫
router.beforeEach((to, from, next) => {
  //...
});

export {
  router
}
```

**在main.ts中引入**
```ts
import { router } from './router';

createApp(App)
 //...
 .use(router)
 //...
```

## 3、Element-Plus与按需加载
```bash
yarn add element-plus -S
```
按需加载的话，还需要使用额外的插件，可以使用官网的按需加载，这里使用尤雨溪推荐的`unplugin-vue-components`插件
```
```bash
yarn add unplugin-vue-components -D
```
修改vite.config.ts配置
```ts
import Components from 'unplugin-vue-components/vite';
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers';

export default defineConfig({
  plugins: [
    vue(),
    Components({
      resolvers: [
        ElementPlusResolver(),
      ]
    })
  ],
  //....
});
```
这样就可以直接在template中使用element-plus组件，而不需要每次都手动引入。

**使用自动引入遇到的一系类问题**
### 1. 自动引入，控制台警告
问题`[vite] Failed to load source map for /node_modules/.vite/chunk-CCWPAK2K.js?v=XXXXXXXX.`
or
`[vite]:new dependencies found: ant-design-vue/es/spin/style/css, ant-design-vue/es/select/style/css, updating...`

报错中提示我们`node_modules`下的.vite中没有xxxx.js文件，我翻看.vite目录，发现只有一个package.json文件，而没有xxxx.js，所以会报错

排除的方法：
1、输出node_modules，重新安装依赖
2、第三方插件的影响，只要是新增插件重新安装node_modules都会出现这个问题
3、自动引入是不是不支持手动引入组件，注释项目中的手动引入，发现没有影响

最后，试了一下将自动引入的插件去掉，使用全局引入的方法，发现没有报错，可以得知是由于自动引入插件没有找到依赖而产生的问题。

>其实是没有影响的，这些警告会在一段时间的自动修复后，不会在出现。当页面需要跳转时可以暂时把判断逻辑注释，不要拦截让其先跳转最简单的页面布局，自动修复后再复原页面。

### 2、按需引入ElMessageBox样式丢失
```ts
// 单独引入ElMessageBox
import { ElMessageBox } from 'element-plus'
// 单独引入样式即可
import 'element-plus/theme-chalk/el-message-box.css';
```

### 3、Element菜单折叠卡顿
**编写项目导航栏折叠过程中，发现折叠时菜单发生卡顿(图标闪烁), 影响用户体验**

需要加上一些样式进行处理,样式如下
，同时设置菜单宽度也在这个CSS里面设置，效果完美
```scss
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  height: 100%;
}
```

### 4、打包报错,查找报错信息发现是element-plus相关的错误
**解决方法**
在 `tsconfig.json` 文件中 `compilerOptions` 选项下加入 `"skipLibCheck": true` // 跳过自由检查
```json
{
  "compilerOptions": {
    "skipLibCheck": true,
  }
}
```

### 5、Ts引入第三方包报错：“无法找到模块”
解决办法有两种：
方法一：安装库的TypeScript声明文件
方法二：添加自己写库的TypeScript类型声明
在方法一可行的情况下，推荐使用方法一，但是不是所有库都有 TypeScript 的声明文件，所以方法一无效才用方法二。

**方法一：**
根据报错提示哪个库缺TypeScript声明文件安装对应的就是了
```bash
npm install -D @types/库的名字
```

**方法二：自己添加特定库的ts类型声明**
新建types类型文件夹,添加index.d.ts类型文件
```ts
declare module 'xxxxxx';
```