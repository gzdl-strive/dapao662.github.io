---
title: 下载JSON文件
date: 2022-08-11 10:25:24
toc: true
---

>下载json文件
>1、模拟下载，可以通过新建一个标签并设置url及download属性来下载
>2、可以通过把json转化为dataurl来构造URL
>3、可以通过把json转换为Blob在转换为ObjectURL来构造URL

```js
function download(url, name) {
  const a = document.createElement('a');
  a.download = name;
  a.rel="打开失败";
  a.href = url;
  // 触发模拟点击
  a.dispatchEvent(new MouseEvent('click'));
  // 或者a.click();
}

const json = {
  name: '张三',
  age: 20
};

const str = JSON.stringify(json, null, 2);
// 方法一、Text -> DataURL
const dataUrl = `data:,${str}`;
download(dataUrl, 'demo.json');

// 方法二、Text -> Blob -> ObjectURL
const objUrl = URL.createObjectURL(new Blob(str.split('')));
download(objUrl, 'demo.json');
```