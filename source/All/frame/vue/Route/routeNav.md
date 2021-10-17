---
title: 编程式路由导航
toc: true
date: 2021-10-15 11:29:02
---


### 8.```<router-link>```的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为```push```和```replace```，```push```是追加历史记录，```replace```是替换当前记录。路由跳转时候默认为```push```
3. 如何开启```replace```模式：```<router-link replace .......>News</router-link>```


### 编程路由导航
>作用：不借助```<router-link> ```实现路由跳转，让路由跳转更加灵活

2. 具体编码：

```js
  //$router的两个API
  this.$router.push({
    name:'xiangqing',
    params:{
      id:xxx,
      title:xxx
    }
  })

  this.$router.replace({
    name:'xiangqing',
    params:{
      id:xxx,
      title:xxx
    }
  })
  this.$router.forward() //前进
  this.$router.back() //后退
  this.$router.go() //可前进也可后退
```