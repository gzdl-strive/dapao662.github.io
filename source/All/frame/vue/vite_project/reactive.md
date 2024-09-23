---
title: Vue3认识Reactive全家桶
date: 2022-03-22 14:26:24
toc: true
---

### reactive
>用来绑定复杂的数据类型，例如对象、数组
>如果使用ref绑定复杂数据类型，其底层也是使用reactive

reactive源码限制了我们的类型
```ts
export function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
```
它是不可以绑定普通的数据类型
- 绑定普通的数据类型 我们可以 使用ref
- 你如果用ref去绑定对象 或者 数组 等复杂的数据类型 我们看源码里面其实也是 去调用reactive
- 使用reactive 去修改值无须.value

**数组异步赋值问题**
>这样赋值页面是不会变化，因为会脱离响应式
```js
let person = reactive<number[]>([]);

setTimeout(() => {
  person = [1, 2, 3];
  console.log(person);
}, 1000)
```

**解决方法一、使用push**
```js
let person = reactive<number[]>([]);

setTimeout(() => {
  const arr = [1, 2, 3];
  person.push(...arr)
}, 1000)
```

**解决方法二、包裹一层对象**
```js
type Person = {
  list?:Array<number>
}
let person = reactive<Person>({
   list:[]
})
setTimeout(() => {
  const arr = [1, 2, 3]
  person.list = arr;
},1000)
```

### readonly
>拷贝一份proxy对象将其设置为只读
```js
import { reactive, readonly } from 'vue'
const person = reactive({
  count: 1,
});
const copy = readonly(person);

person.count++;
copy.count++;//报错
```
>如果任何 property 使用了 ref，当它通过代理访问时，则被自动解包
```js
const raw = {
  count: ref(123)
}
const copy = readonly(raw)
console.log(raw.count.value) // 123
console.log(copy.count) // 123
```

### shallowReactive
>只能对浅层的数据，如果是深层的数据只会改变值，不会改变视图

**只有在DOM挂载后再去改变深层次的数据，视图不会改变，如果在DOM挂载前修改，视图还是会变为最新的值**
