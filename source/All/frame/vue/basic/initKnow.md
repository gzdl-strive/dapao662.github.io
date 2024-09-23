---
title: 初识Vue
date: 2021-10-08 09:36:24
toc: true
---

>插值语法`{{}}`：里面要写js表达式

## js表达式和js代码(语句)

### js表达式
>一个表达式会产生一个值，可以放在任何一个需要值的地方:
```js
  (1). a
  (2). a + b
  (3). demo(1)
  (4). x === y ? 'a' : 'b'
```

### js代码(语句)
```js
  (1). if(){}
  (2). for(){}
```

### 初步应用Vue
```js
  // Vue.config.devtools = false; //配置是否允许 vue-devtools 检查代码
  Vue.config.productionTip= false;//阻止vue在启动时生成生产提示

  //创建Vue实例
  new Vue({
    el: '#app',//el用于指定当前Vue实例为哪个容器服务，值通常为css选择器字符串
    //data中用于存储数据，数据供el所指定的容器去使用
    data() {
      return {
        msg: 'Hello World!'
      }
    }
  })
```