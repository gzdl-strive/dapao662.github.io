---
title: 路由props配置
toc: true
date: 2021-10-15 11:25:02
---

>作用：让路由组件更方便的收到参数

```js
{
  name: 'xingqing',
  path: 'detail/:id',
  component: Detail,
  //第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
	// props:{a:900}
  //第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
	// props:true
  //第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
  props(route){
		return {
			id:route.query.id,
			title:route.query.title
		}
	}
}
```