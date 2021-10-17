---
title: getters
date: 2021-10-14 15:30:24
toc: true
---

>概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。

>在```store.js```中追加```getters```配置
```js
  ......

  const getters = {
    bigSum(state){
      return state.sum * 10
    }
  }

  //创建并暴露store
  export default new Vuex.Store({
  ......
    getters
  })
```

>组件中读取数据：```$store.getters.bigSum```
