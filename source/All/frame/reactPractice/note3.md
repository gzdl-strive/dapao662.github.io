---
title: Router笔记
date: 2021-07-20 10:28:24
toc: true
---

>`npm install react-router-dom -D`

## 基本使用
```js
import { BrowserRouter as Router, Route, Redirect, Link } from "react-router-dom";

const Home = () => <div>Hello</div>

//使用
// {/* 加了Switch: 排他性路由（只能匹配一个）  不加：包容性路由*/}
<Switch>
  <Router>
    <ul>
      <li>
        <Link to="/">Home</Link>
      </li>
    </ul>
    <Route path="/" exact component={Home}></Route>
    // 重定向
    <Redirect  from="/" to="/home"/>
  </Router>
</Switch> 
```
