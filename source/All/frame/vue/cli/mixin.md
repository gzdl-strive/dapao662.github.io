---
title: mixin(混入/混合)
toc: true
date: 2021-10-13 17:44:24
---

## mixin(混入)
1. 功能：可以把多个组件共用的配置提取成一个混入对象
2. 使用方式：
    第一步定义混合：
    ```
    {
        data(){....},
        methods:{....}
        ....
    }
    ```
    第二步使用混入：
    ​	全局混入：```Vue.mixin(xxx)```
    ​	局部混入：```mixins:['xxx']	```

```js
//mixin.js
export const mixin = {
  methods: {
    showName() {
      alert(this.name);
    },
  },
  mounted() {
    console.log('Hello Vue');
  }
}
export const mixin2 = {
  data() {
    return {
      x: 100,
      y: 101
    }
  },
}
```

**局部混入**
```html
<!-- xxx.vue -->
<script>
import { mixin, mixin2 } from 'xxxxx/mixin';
export default {
  data() {...},
  //局部混入
  mixins: ['mixin', 'mixin2']
}
</script>
```

**全局混入**
```js
//main.js
import { mixin, mixin2 } from 'xxx/mixin'
//全局混入/混合
Vue.mixin(mixin)
Vue.mixin(mixin2)
```

