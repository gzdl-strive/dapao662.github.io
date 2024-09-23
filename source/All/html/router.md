---
title: hash模式、history模式
date: 2021-05-26 15:58:20
tag: browser
toc: true
---

## 路由
>路由指的是通过不同URL展示不同页面或者内容的功能

路由的概念来源于服务端，在服务端中路由描述的是URL与处理函数之间的映射关系。
在Web前端单页应用SPA(Single Page Application)中，路由描述的是URL与UI之间的映射关系，这种映射是单向的，即URL变化引起UI更新(无需刷新页面)

## 前端路由
**核心**
* 不引起页面刷新
* 检测URL变化

### hash模式
* 监听url中hash的变化，渲染不同的内容
* hash: url尾巴后的#以及后面的字符
 * #代表网页中的一个位置，其右边的字符，就是该位置的标识符。
* HTTP请求中不包含#
 * 也就是说改变#后的内容不会像服务器发起请求，因此也就不会引起页面重载。
 * 比如访问: `http://jquery.com#hello`
 * 浏览器实际发出的请求时这样的：
![hash](/assets/browserImg/hash.png "hash")
* 改变#后的内容
 * 浏览器只会滚动到相应位置，不会重新加载网页
 * 改变#会改变浏览器的访问历史
  * 使用"后退"按钮，就可以回到上一个位置
* window.location.hash
 * 读取时，可以用来判断网页状态是否改变
 * 写入时，则会在不重载网页的前提下，创造一条访问历史记录
* 每次hash发生改变是，会触发hashchange事件
```js
window.location.hash = "qq";//设置url的hash,会在当前url后加上"#qq";
let hash = window.location.hash;
window.addEventerListener('hashchange', function () {
    //...
})
```

### history模式
* 监听 url 中的路径变化，需要客户端和服务端共同的支持
* h5之前，通过 history 对象能够访问浏览器历史记录    ---前进/后退
 * length 属性可以访问历史记录对象的长度
 * forward 和 back 方法如同浏览器的前进和后退功能
 * go 方法可以通过指定一个相对于当前页面的索引值来跳转到相应的记录。(正进负退)
* h5之后，通过 history 对象能够操纵浏览器历史记录    ---到达指定url
 * pushState 方法    ---添加
    * 能在不刷新页面(不发请求)的前提下，将当前页面地址保存为最近一条历史记录，同时修改当前页面地址
 * replaceState 方法    ---替换
    * 与 pushState 很相似，区别在于是修改当前历史记录(只修改当前页面地址)，而 pushState 是创建新的历史记录
```js
window.history.pushState(state, title, url);
window.history.replaceState(state, title, url);
window.addEventListener("popstate", function () {
    //...
})
```

* popstate事件
 * 触发：
    * 点击浏览器的前进后退按钮
    * 调用history.forward( )方法 或者 history.back( )方法
 * 不触发：
    * 调用history.pushState( )方法 或者 history.replaceState( )方法

* history 在刷新页面时，如果服务器中没有相应的响应或资源，就会出现404。因为刷新浏览器后，是真实地向服务器发送了一个 http 的网页请求。因此若要使用 history 路由，需要服务端的支持。

* hash 的改变会触发 onhashchange 事件，history 的改变却不会触发什么事件，但我们能通过罗列出所有可能改变 history 的途径，然后在这些途径一一进行拦截，这样也相当于监听了 history 的改变。

* 在单页面应用中，URL 改变只能由下面三种情况引起：
 * 点击浏览器的前进或者后退按钮；    ---通过popstate事件监听
 * 点击 a 标签；
 * 调用pushState( )方法 或者 replaceState( )方法
    * 后两种途径可以看成是一种，因为 a 标签的默认事件可以被禁止，进而调用 JS 方法。
    * 即点击a标签也相当于调用了pushState( )方法 或者 replaceState( )方法