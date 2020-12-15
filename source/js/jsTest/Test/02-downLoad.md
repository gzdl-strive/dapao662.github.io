---
title: 下载json文件
date: 2020-12-13 19:12:16
tag: js
---

>* 模拟下载，可以通过新建一个<a href="url" download="xxx"></a>标签并设置 url 及 download 属性来下载
>* 可以通过把json转化为dataurl来构造URL
>* 可以通过把json转换为Blob再转换为ObjectURL来构造URL

```js
function download (url, name) {
    const a = document.createElement('a');
    a.download = name;
    a.rel = '打开失败'
    a.href = url;
    //触发模拟点击
    a.dispatchEvent(new MouseEvent('click'));
    //或者a.click()
}
const json = {
    name: 'xxx',
    age: 20,
    sex: 'man'
}
const str = JSON.stringify(json, null, 2);
//方法一、Text->DataURL
const dataUrl = `data:,${str}`;
download(dataUrl, 'demo.json');
//方法二、Text->Blob->ObjectURL
cosnt ObjURL = URL.createObjectURL(new Blob(str.split('')));
download(ObjURL, 'demo.json');
```