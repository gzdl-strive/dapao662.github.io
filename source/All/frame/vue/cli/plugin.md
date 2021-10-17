---
title: 插件
toc: true
date: 2021-10-13 17:50:24
---

## 插件
> 1. 功能：用于增强Vue
> 2. 本质：包含install方法的一个对象，install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据。
> 3. 定义插件：

    ```js
    对象.install = function (Vue, options) {
        // 1. 添加全局过滤器
        Vue.filter(....)
    
        // 2. 添加全局指令
        Vue.directive(....)
    
        // 3. 配置全局混入(合)
        Vue.mixin(....)
    
        // 4. 添加实例方法
        Vue.prototype.$myMethod = function () {...}
        Vue.prototype.$myProperty = xxxx
    }
    ```

4. 使用插件：```Vue.use()```

## 定义一个插件
```js
//plugins.js
export default {
  //插件接收一个入参，该参数为Vue构造函数
  install(Vue) {
    //过滤器
    Vue.filter('mySlice', function (value) {
      return value.slice(0, 5);
    });
    //自定义指令
    Vue.directive('fbind', {
      bind(element, binding) {
        element.value = binding.value;
      },
      inserted(element) {
        element.focus();
      },
      update(element, binding) {
        element.value = binding.value;
      }
    });
    //混入
    Vue.mixin({
      data() {
        return {
          x: 100,
          y: 101
        }
      }
    });
    //原型方法
    Vue.prototype.hello = function () {
      alert('Hello')
    }
  }
}
```

## 使用插件
```js
//main.js
//引入插件
import plugins from 'xxxx/plugins';

//应用（使用）插件
Vue.use(plugins);
```
