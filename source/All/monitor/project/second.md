---
title: 监控js执行错误和Promise异常
date: 2021-07-14 17:36:24
toc: true
---

## 监控JS执行错误
**因为要监控JS错误，所以，我们首先要创建一个js执行错误**
```html
<!-- index.html文件 -->
<body>
  <div id="container">
    <div id="content">
      <input id="errorBtn" type="button" value="点击抛出错误" onclick="errorClick()"/>
    </div>
  </div>
  <script>
    function errorClick() {
      window.someVar.error = 'error';//因为没有someVar这个全局变量，所以会抛出错误
    }
  </script>
</body>
```

- 1、在src目录下创建monitor文件夹，在monitor文件夹下新建index.js文件和lib文件夹和utils文件夹，在lib文件夹下新建jsError.js文件

- 2、src下的index文件如下
```js
import './monitor';//引入的是monitor文件夹下的index.js文件
```

- 3、monitor文件夹下的index.js文件内容如下
```js
import { injectJsError } from './lib/jsError';
injectJsError();
```
**该文件引入了lib下的jsError,在jsError.js中我们来进行监控相关的事情**

- 4、我们还需要创建几个工具函数，用于辅助我们获取错误事件
**第一个：获取最后一个交互事件(在utils下创建getLastEvent.js)**
```js
//getLastEvent.js文件->用于获取最后一个交互事件
let lastEvent;
let errorEvent = ['click', 'touchstart', 'mousedown', 'keydown', 'mouseover'];
errorEvent.forEach(eventType => {
  document.addEventListener(eventType, (event) => {
    lastEvent = event;
  }, {
    capture: true,//捕获阶段执行
    passive: true//默认不阻止默认事件
  });
})
export default function() {
  return lastEvent;
}
```
**第二个:当我们获取到错误的对象是，我们需要把该DOM节点的位置拼接起来并返回(在utils下创建getSelector.js文件)**
```js
function getSelectors(path) {
  return path.reverse().filter(element => {
    return element !== document && element !== window//去掉document和window节点
  }).map(element => {
    let selector = '';
    if (element.id) {
      return `${element.nodeName.toLowerCase()}#${element.id}`;
    } else if (element.className && typeof element.className === 'string') {
      return `${element.nodeName.toLowerCase()}.${element.className}`;
    } else {
      selector = element.nodeName.toLowerCase();
    }
    return selector;//返回处理后的数组
  }).join(' ');//拼接成字符串
}
export default function (pathOrTarget) {
  if (Array.isArray(pathOrTarget)) {//可能是一个数组
    return getSelectors(pathOrTarget);
  } else {
    //也有可能是一个对象
    let path = [];
    while (pathOrTarget) {
      path.push(pathOrTarget);
      pathOrTarget = pathOrTarget.parentNode;
    }
    return getSelectors(path);
  }
}
```
**第三个：tracker(用于发送错误日志至阿里云日志中),在utils中创建tracker.js文件**
```js
//tracker.js
let host = 'cn-hangzhou.log.aliyuncs.com';
let projectName = 'testmonitor';//创建的项目名字
let logStore = 'testmonitor-store';//项目里的存储名字
let userAgent = require('user-agent');

function getExtraData() {
  return {
    title: document.title,
    url: location.href,
    timestamp: Date.now(),
    userAgent: userAgent.parse(navigator.userAgent).name,
    //...
  }
}

class SendTracker {
  constructor() {
    this.url = `http://${projectName}.${host}/logstores/${logStore}/track`;//上报路径
    this.xhr = new XMLHttpRequest;
  }
  send(data = {}) {
    let extraData = getExtraData();
    let log = { ...extraData, ...data };
    //对象的值不能是数字->阿里云要求
    for (let key in log) {
      if (typeof log[key] === 'number') {
        log[key] = `${log[key]}`;//转成字符串
      }
    } 
    let body = JSON.stringify({
      __logs__: [log]
    });
    this.xhr.open('POST', this.url, true);
    this.xhr.setRequestHeader('Content-Type', 'application/json');//请求体类型
    this.xhr.setRequestHeader('x-log-apiversion', '0.6.0');//版本号
    this.xhr.setRequestHeader('x-log-bodyrawsize', body.length);//请求体大小
    this.xhr.onerror = function (error) {
      console.log(error);
    }
    this.xhr.send(body);
  }
}
export default new SendTracker();
```

- 5、jsError.js文件
```js
import getLastEvent from '../utils/getLastEvent';
import getSelector from '../utils/getSelector';
import tracker from '../utils/tracker';
//导出injectJsError
export function injectJsError() {
  //监听全局未捕获的错误
  window.addEventListener('error', function(event) {//错误事件对象
    let lastEvent = getLastEvent();//获得最后一个交互事件
    tracker.send({
      kind: 'stability',//监控指标的大类
      type: 'error',//小类型，这是一个错误
      errorType: 'jsError',//JS执行错误
      message: event.message,//报错信息
      filename: event.filename,//哪个文件报错了
      position: `${event.lineno}行:${event.colno}列`,
      selector: laseEvent ? getSelector(lastEvent.path) : '',//代表最后一个操作的元素
    })；
  }, true);
}
```



