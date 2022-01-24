---
title: 什么是模板引擎
date: 2022-01-07 10:02:24
toc: true
---

> 模板引擎是将数据变为视图最优雅的解决方案

![1](/assets/vueImg/vueSource/mustache1.png "什么是模板引擎")

## 历史上曾经出现的数据变为视图的方法

### 1、纯 DOM 法

```js
//html结构: <ul id="list"></ul>
let arr = [
  { name: "小明", age: 12, sex: "男" },
  { name: "小红", age: 11, sex: "女" },
  { name: "小强", age: 13, sex: "男" },
];

let list = document.getElementById("list");

for (let i = 0; i < arr.length; i++) {
  //每遍历一项，都要用DOM去创建li标签
  let oLi = document.createElement("li");
  //创建hd这个div
  let hdDiv = document.createElement("div");
  hdDiv.className = "hd";
  hdDiv.innerText = arr[i].name + "的基本信息";
  //创建bd这个div
  let bdDiv = document.createElement("div");
  bdDiv.className = "bd";
  // 创建三个p
  let p1 = document.createElement("p");
  p1.innerText = "姓名：" + arr[i].name;
  bdDiv.appendChild(p1);
  let p2 = document.createElement("p");
  p2.innerText = "年龄：" + arr[i].age;
  bdDiv.appendChild(p2);
  let p3 = document.createElement("p");
  p3.innerText = "性别：" + arr[i].sex;
  bdDiv.appendChild(p3);
  // 创建的节点是孤儿节点，所以必须要上树才能被用户看见
  oLi.appendChild(hdDiv);
  // 创建的节点是孤儿节点，所以必须要上树才能被用户看见
  oLi.appendChild(bdDiv);
  // 创建的节点是孤儿节点，所以必须要上树才能被用户看见
  list.appendChild(oLi);
}
```

**非常笨拙**

### 2、数组 join 法

```js
let arr = [
  { name: "小明", age: 12, sex: "男" },
  { name: "小红", age: 11, sex: "女" },
  { name: "小强", age: 13, sex: "男" },
];

let list = document.getElementById("list");

// 遍历arr数组，每遍历一项，就以字符串的视角将HTML字符串添加到list中
for (let i = 0; i < arr.length; i++) {
  list.innerHTML += [
    "<li>",
    '    <div class="hd">' + arr[i].name + "的信息</div>",
    '    <div class="bd">',
    "        <p>姓名：" + arr[i].name + "</p>",
    "        <p>年龄：" + arr[i].age + "</p>",
    "        <p>性别：" + arr[i].sex + "</p>",
    "    </div>",
    "</li>",
  ].join("");
}
```

### 3、ES6 反引号法

```js
let arr = [
  { name: "小明", age: 12, sex: "男" },
  { name: "小红", age: 11, sex: "女" },
  { name: "小强", age: 13, sex: "男" },
];

let list = document.getElementById("list");

// 遍历arr数组，每遍历一项，就以字符串的视角将HTML字符串添加到list中
for (let i = 0; i < arr.length; i++) {
  list.innerHTML += `
    <li>
      <div class="hd">${arr[i].name}的基本信息</div>    
      <div class="bd">
        <p>姓名：${arr[i].name}</p>    
        <p>性别：${arr[i].sex}</p>    
        <p>年龄：${arr[i].age}</p>    
      </div>    
    </li>
  `;
}
```

### 4、模板引擎
>模板引擎的使用在下一文中使用
