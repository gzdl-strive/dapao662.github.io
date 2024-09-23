---
title: 路由守卫
toc: true
date: 2021-10-17 13:10:24
---

> 作用：对路由进行权限控制

> 分类:
>
> > 1、全局守卫
> > 2、独享守卫
> > 3、组件内守卫

### 1、全局守卫

```js
//  router/index.js
//...
const router = new VueRouter({
  routes: [
    {
      name: "xxx",
      path: "yyy",
      component: AAA,
      meta: { title: "xxxxxx", isAuth: true },
    },
    //...
  ],
});

//全局前置路由守卫————初始化的时候被调用、每次路由切换之前被调用
router.beforeEach((to, from, next) => {
  console.log("前置路由守卫");
  if (to.meta.isAuth) {
    //判断是否需要鉴权
    if (localStorage.getItem("user") === "s1mple") {
      //权限控制的具体规则
      next(); //放行
    } else {
      alert("暂无权限查看");
    }
  } else {
    //不需要鉴权
    next(); //放行
  }
});

//全局后置路由守卫: 初始化时执行、每次路由切换后执行
router.afterEach((to, from, next) => {
  console.log("后置路由守卫");
  if (to.meta.title) {
    document.title = to.meta.title; //修改网页的title
  } else {
    document.title = "vue_test";
  }
});
```

### 2、独享守卫

```js
//  router/index.js
//...
const router = new VueRouter({
  routes: [
    {
      name: 'xxx',
      path: '/yyy',
      component: AAA,
      meta: {title: 'xxxxxx', isAuth:: true},
      beforeEnter: (to, from, next) => {
        console.log('独享路由守卫');
        if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
          if(localStorage.getItem('school') === 's1mple'){
            next()
          }else{
            alert('暂无权限查看')
          }
        } else {
          next(); //放行
        }
      }
    },
    //...
  ]
})
```

### 3、组件内守卫

```js
// xxx.vue

export default {
  //进入守卫：通过路由规则，进入该组件时被调用
  beforeRouteEnter(to, from, next) {
    console.log("About--beforeRouteEnter", to, from);
    if (to.meta.isAuth) {
      //判断是否需要鉴权
      if (localStorage.getItem("user") === "s1mple") {
        next();
      } else {
        alert("用户名不对，无权限查看！");
      }
    } else {
      next();
    }
  },
  //离开守卫：通过路由规则，离开该组件时被调用
  beforeRouteLeave (to, from, next) {
    console.log('About--beforeRouteLeave',to,from)
    next()
  }
};
```
