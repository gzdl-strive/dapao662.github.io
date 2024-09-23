---
title: React路由
date: 2021-12-10 14:47:24
toc: true
---

## 相关理解

### 1、SPA 的理解

1. 单页 Web 应用（`single page web application，SPA`）。
2. 整个应用只有一个完整的页面。
3. 点击页面中的链接不会刷新页面，只会做页面的局部更新。
4. 数据都需要通过 ajax 请求获取, 并在前端异步展现。

### 2、路由的理解

**什么是路由?**

1. 一个路由就是一个映射关系(key:value)
2. key 为路径, value 可能是 function 或 component

**路由分类**

- 后端路由：
  (1)理解： value 是 function, 用来处理客户端提交的请求。
  (2)注册路由： router.get(path, function(req, res))
  (3)工作过程：当 node 接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据

- 前端路由： 1)浏览器端路由，value 是 component，用于展示页面内容。 2)注册路由: `<Route path="/test" component={Test}>` 3)工作过程：当浏览器的 path 变为/test 时, 当前路由组件就会变为 Test 组件

### 3、react-router-dom 的理解

1. react 的一个插件库。
2. 专门用来实现一个 SPA 应用。
3. 基于 react 的项目基本都会用到此库。

## react-router-dom 相关 API

### 路由基本使用

1. 明确好界面中的导航区、展示区
2. 导航区的 a 标签改为 Link 标签
   `<Link to="/xxxxx">Demo</Link>`
3. 展示区写 Route 标签进行路径的匹配
   `<Route path='/xxxx' component={Demo}/>`
4. `<App>`的最外侧包裹了一个`<BrowserRouter>`或`<HashRouter>`

### 路由组件与一般组件
1. 写法不同：
  一般组件：`<Demo/>`
  路由组件：`<Route path="/demo" component={Demo}/>`
2. 存放位置不同：
  一般组件：components
  路由组件：pages
3. 接收到的props不同：
  一般组件：写组件标签时传递了什么，就能收到什么
  路由组件：接收到三个固定的属性
```js
/*
  history:
    go: ƒ go(n)
    goBack: ƒ goBack()
    goForward: ƒ goForward()
    push: ƒ push(path, state)
    replace: ƒ replace(path, state)
  location:
        pathname: "/about"
        search: ""
        state: undefined
  match:
        params: {}
        path: "/about"
        url: "/about"
*/
```

### 内置组件

**1、`<BrowserRouter>`**
**2、`<HashRouter>`**
**3、`<Route>`**
**4、`<Redirect>`**
**5、`<Link>`**
**6、`<NavLink>`**

1. NavLink可以实现路由链接的高亮，通过activeClassName指定样式名(默认类目是active)
2. 标签体内容是一个特殊的标签属性(children)
3. 通过this.props.children可以获取标签体内容

**封装 NavLink**

```js
import React, { Component } from "react";
import { NavLink } from "react-router-dom";
export default class MyNavLink extends Component {
  render() {
    // console.log(this.props);
    return (
      <NavLink
        activeClassName="atguigu"
        className="list-group-item"
        {...this.props}
      />
    );
  }
}
```

**7、`<Switch>`**

1. 通常情况下，path和component是一一对应的关系。
2. Switch可以提高路由匹配效率(单一匹配)。

### 解决多级路径刷新页面样式丢失的问题
（webpack配置了，项目根路径为public目录）
（如果请求的路径不存在，会把public下的index.html显示出来）
1. public/index.html 中 引入样式时不写 ./ 写 / （常用）
2. public/index.html 中 引入样式时不写 ./ 写 `%PUBLIC_URL%` （常用）
3. 使用HashRouter

### 路由的严格匹配与模糊匹配
1. 默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）
2. 开启严格匹配：`<Route exact={true} path="/about" component={About}/>`
3. 严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由

### Redirect的使用	
1.一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由
2.具体编码：
```js
<Switch>
  <Route path="/about" component={About}/>
  <Route path="/home" component={Home}/>
  <Redirect to="/about"/>
</Switch>
```

### 嵌套路由
1. 注册子路由时要写上父路由的path值
2. 路由的匹配是按照注册路由的顺序进行的（路由的注册都是从最开始注册的路由开始匹配的--》模糊匹配）

### 向路由组件传递参数
1. params参数
  路由链接(携带参数)：`<Link to='/demo/test/tom/18'}>详情</Link>`
  注册路由(声明接收)：`<Route path="/demo/test/:name/:age" component={Test}/>`
  接收参数：this.props.match.params
2. search参数
  路由链接(携带参数)：`<Link to='/demo/test?name=tom&age=18'}>详情</Link>`
  注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`
  接收参数：this.props.location.search
  备注：获取到的search是urlencoded编码字符串，需要借助querystring解析
3. state参数
  路由链接(携带参数)：`<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>`
  注册路由(无需声明，正常注册即可)：`<Route path="/demo/test" component={Test}/>`
  接收参数：this.props.location.state
  备注：刷新也可以保留住参数

### 其他

**1、history 对象**
**2、match 对象**
**3、withRouter 函数**

### 编程式路由导航
借助this.prosp.history对象上的API对操作路由跳转、前进、后退
- this.prosp.history.push()
- this.prosp.history.replace()
- this.prosp.history.goBack()
- this.prosp.history.goForward()
- this.prosp.history.go()

### withRouter使用
withRouter可以加工一般组件，让一般组件具备路由组件所特有的API,withRouter的返回值是一个新组件
```js
import React, { Component } from 'react'
import {withRouter} from 'react-router-dom'

class Header extends Component {
  back = ()=>{
		this.props.history.goBack()
	}
  render() {
    //...
  }
}

export default withRouter(Header)
```

### BrowserRouter与HashRouter的区别
1. 底层原理不一样：
  BrowserRouter使用的是H5的history API，不兼容IE9及以下版本。
  HashRouter使用的是URL的哈希值。
2. path表现形式不一样
  BrowserRouter的路径中没有#,例如：localhost:3000/demo/test
  HashRouter的路径包含#,例如：localhost:3000/#/demo/test
3. 刷新后对路由state参数的影响
  (1).BrowserRouter没有任何影响，因为state保存在history对象中。
  (2).HashRouter刷新后会导致路由state参数的丢失！！！
4. 备注：HashRouter可以用于解决一些路径错误相关的问题。(多级路由刷新样式丢失问题)