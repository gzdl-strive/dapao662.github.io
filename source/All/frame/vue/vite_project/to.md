---
title: Vue3认识To全家桶
date: 2022-03-22 14:29:24
toc: true
---

### toRef
>如果原始对象是非响应式的就不会更新视图 数据是会变的
>如果原始对象是响应式的是会更新视图并且改变数据的
### toRefs
>可以版我们批量创建ref对象，主要是方便我们解构使用
```js
import { reactive, toRefs } from 'vue'
const obj = reactive({
   foo: 1,
   bar: 1
})
 
let { foo, bar } = toRefs(obj)
 
foo.value++
console.log(foo, bar);
```

### toRaw
>将响应式对象转为普通对象
