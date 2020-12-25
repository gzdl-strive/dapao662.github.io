---
title: 第十二章-BOM
date: 2020-12-25 09:05:10
tag: js
---

>像素比-`window.devicePixelRatio`--物理像素和css像素之间的转换比率

> `window.open()`跳转到新窗口-三个参数:要跳转的url、目标窗口、特性字符串
**特性字符串是一个用逗号分隔的设置字符串，用于指定新窗口包含的特性**
```js
/*
设置            值            说明
fullscreen  "yes"或"no"     表示新窗口是否最大化，仅限ie支持
height          数值        新窗口高度，不能小于100
left            数值        新窗口x轴坐标,不能是负数
location     "yes"或"no"    表示是否隐藏地址栏。不同浏览器默认值不同
Menubar     "yes"或"no"     表示是否显示菜单栏,默任为"no"
resizable   "yes"或"no"     表示是否可以拖动改变新窗口大小，默认为"no"
scrollbars  "yes"或"no"     表示是否可以在内容过长时滚动。默认为"no"
status      "yes"或"no"     表示是否显示状态栏
toolbar     "yes"或"no"     表示是否显示工具栏
top             数值        新窗口y轴坐标.不能是负数
width           数值        新窗口宽度，不能小于100
*/
window.open('http://www.baidu.com', '百度', 'height=400,width=400,top=10,left=10,resizable=yes,Menubar="yes"')
//例子
let wrowin = window.open('http://www.baidu.com', "百度", "top=0,width=200,height=200,left=100")
wrowin.resizeTo(500,500);
wrowin.moveTo(100,200);
```
**还可以使用`window.close`关闭新打开的窗口**
```js
wrowin.close();// 只能用于window.open创建的弹出窗口
```

>系统对话框alert()、confirm()、prompt()、find()、print()

>`location`对象-`location`是BOM对象最有用的对象之一,提供了当前窗口中加载文档的信息，以及通常的导航功能
**独特之处--即是window的属性也是document的属性**
```js
// 假设当前URL是: http://foouser:barpassword@www.wrox.com:80/WileyCDA/?q=javascript#content
/*
属性                          值                    说明
location.hash           "#contents"             URL散列值(#后跟0或多个字符)，如果没有则为空字符串
location.host           "www.wrox.com:80"       服务器名及端口号
location.hostname       "www.wrox.com"          服务器名
location.href           http://foouser:barpassword@www.wrox.com:80/WileyCDA/?q=javascript#content 完整url
location.pathname       "/WileyCDA"             URL中的路径和(或)文件名
location.port           “80”                    请求的端口
location.protocol       "http:"                 页面使用的协议
location.search         "?q=javascript"         URL的查询字符串,以?开头
location.username       "foouser"               域名前指定的用户名
location.password       "barpassword"           域名前指定的密码
location.origin         "http://www.wrox.com"   URL源地址。只读
*/
```
**`location.search`虽然返回url的参数，但没法逐个访问查询参数**
**查询字符串**
```js
let getQueryStringArgs = function() {
    let qs = (location.search.length > 0 ? location.search.substring(1) : "");
    let args = {};
    for (let item of qs.split("&").map(fn => fn.split('='))) {
        let name = decodeURIComponent(item[0]);
        let value = decodeURIComponent(item[1]);
        if (name.length) {
            args[name] = value;
        }
    }
    return args;
}
```
**操作地址--可以通过修改`location`对象来修改浏览器地址.**
>最常见的`assign()方法`传入一个url
```js
location.assign("http://www.baidu.com")//立即启动到新url
```
**如果给`window.location`或`document.location`设置一个url，也会以同一个url调用`assign`方法-修改`location属性`也会修改当前加载的页面**
```js
location.hash = "#section1";
location.search = "?p=javascript1";
location.hostname = "xxx"
location.pathname = "xxx"
location.port = 9000;
// 除hash外，修改location的一个任一属性，就会导致页面重新加载新URL
//用以上方法修改url后，可以回退到前一个页面，如果不希望回退，可以使用replace方法
//最后一个修改地址的方法是reload(),他能重新加载当前显示的页面
```

>navigator对象
>screen对象

> histroy对象

**`go()方法`-可以在用户历史记录中沿任何方法导航**
```js
//后退一页
history.go(-1);
//前进一页
history.go(1);
//go有两个简写方法back()和forward()
history.back()//后退一页
history.forward()//前进一页
history.length//表示历史记录中有多少条目
```