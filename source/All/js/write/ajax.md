---
title: AJAX
date: 2022-08-06 18:52:24
toc: true 
----

>XMLHttpRequest是AJAX的基础，可以解释为可扩展超文本传输请求

```js
const getJSON = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = XMLHttpRequest ? new XMLHttpRequest() : new ActiveXObject('Mscrosoft.XMLHttp');
    // method、url、async(true异步，false同步)
    xhr.open('GET', url, false);

    // setRequestHeader(label, value): 将标签/值对添加到要发送的HTTP标头
    xhr.setRequestHeader('Accept', 'application/json');
    // onreadystatechange: 存储函数，每当readyState的属性改变时，就会调用该函数。
    xhr.onreadystatechange = function () {
      // readyState：存有的XHR的状态从0到4发生变化
      // 0 -> 请求未初始化
      // 1 -> 服务器连接已建立
      // 2 -> 请求已接收
      // 3 -> 请求处理中
      // 4 -> 请求已完成，且响应已就绪
      if (xhr.readyState !== 4) {
        return;
      }
      // status: 将状态返回为数字
      // statusText: 以字符串形式返回状态
      if (xhr.status === 200 || xhr.status === 304) {
        // responseText -> 字符串形式的响应数据
        // responseXML -> XML形式的响应数据
        resolve(xhr.responseText);
      } else {
        reject(new Error(xhr.responseText))
      }
    }
    // 将请求发送到服务器
    // 参数string --> 仅用于POST请求
    xhr.send();
  });
}
```

其他方法
- 1、`abort()` -> 取消当前请求
- 2、`getAllResponseHeaders()` -> 已字符串形式返回完整的HTTP标头集
- 3、`getResponseHeader(headerName)`-> 返回指定HTTP标头的值