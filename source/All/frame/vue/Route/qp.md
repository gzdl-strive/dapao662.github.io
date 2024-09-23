---
title: 路由传参
toc: true
date: 2021-10-15 11:21:02
---

### query参数
- 传递参数
```html
<!-- 跳转并携带query参数，to的字符串写法 -->
  <router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link>
        
  <!-- 跳转并携带query参数，to的对象写法 -->
  <router-link 
  :to="{
    path:'/home/message/detail',
    query:{
      id:666,
      title:'你好'
    }
  }"
  >跳转</router-link>
```

2. 接收参数：
```js
$route.query.id
$route.query.title
```

### params参数
1. 配置路由，声明接收params参数
```js
{
  path:'/home',
  component:Home,
  children:[
    {
      path:'news',
      component:News
    },
    {
      component:Message,
      children:[
        {
          name:'xiangqing',
          path:'detail/:id/:title', //使用占位符声明接收params参数
          component:Detail
        }
      ]
    }
  ]
}
```

2. 传递参数

```html
<!-- 跳转并携带params参数，to的字符串写法 -->
<router-link :to="/home/message/detail/666/你好">跳转</router-link>
      
<!-- 跳转并携带params参数，to的对象写法 -->
<router-link 
:to="{
  name:'xiangqing',
  params:{
    id:666,
    title:'你好'
  }
}"
>跳转</router-link>
```

> 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

3. 接收参数：

```js
$route.params.id
$route.params.title
```
