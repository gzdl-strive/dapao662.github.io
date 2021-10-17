---
title: 多级路由/命名路由
toc: true
date: 2021-10-15 11:13:02
---

### 多级路由
1. 配置路由规则，使用children配置项：

```js
routes:[
  {
    path:'/about',
    component:About,
  },
  {
    path:'/home',
    component:Home,
    children:[ //通过children配置子级路由
      {
        path:'news', //此处一定不要写：/news
        component:News
      },
      {
        path:'message',//此处一定不要写：/message
        component:Message
      }
    ]
  }
]
```

2. 跳转（要写完整路径）：
```vue
<router-link to="/home/news">News</router-link>
```

### 命名路由
>作用：可以简化路由的跳转

- 如何使用
1. 给路由命名：
```js
  {
    path:'/demo',
    component:Demo,
    children:[
      {
        path:'test',
        component:Test,
        children:[
          {
            name:'hello' //给路由命名
            path:'welcome',
            component:Hello,
          }
        ]
      }
    ]
  }
```

2. 简化路由
```html
<!-- 简化前，需要写完整的路径 -->
<router-link to="/demo/test/welcome">跳转</router-link>
<!-- 简化后，直接通过名字跳转 -->
<router-link :to="{name: 'hello'}">跳转</router-link>
```