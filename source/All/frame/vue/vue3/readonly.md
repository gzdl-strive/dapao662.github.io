---
title: readonly与shallowReadonly
date: 2021-10-17 18:22:24
toc: true
---

- readonly: 让一个响应式数据变为只读的（深只读）。
- shallowReadonly：让一个响应式数据变为只读的（浅只读）。
- 应用场景: 不希望数据被修改时。

```js
	import {ref,reactive,toRefs,readonly,shallowReadonly} from 'vue'
	export default {
		name: 'Demo',
		setup(){
			//数据
			let sum = ref(0)
			let person = reactive({
				name:'张三',
				age:18,
				job:{
					j1:{
						salary:20
					}
				}
			})

			person = readonly(person)
			// person = shallowReadonly(person)
			// sum = readonly(sum)
			// sum = shallowReadonly(sum)

			//返回一个对象（常用）
			return {
				sum,
				...toRefs(person)
			}
		}
	}
```