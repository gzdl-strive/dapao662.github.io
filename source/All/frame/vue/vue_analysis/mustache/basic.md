---
title: Mustache基本使用
date: 2022-01-07 10:41:24
toc: true
---

> mustache 是“胡子”的意思，因为它的嵌入标记`{{ }}`非常像胡子,没错，`{{ }}`的语法也被 Vue 沿用，这就是我们学习 mustache 的原因

mustache 是最早的模板引擎库，比 Vue 诞生的早多了，它的**底层实现机理在当时是非常有创造性的、轰动性的**，为后续模板引擎的发展提供了崭新的思路

### 循环对象数组

```html
<div id="container"></div>

<!-- 模板 -->
<script type="text/template" id="mytemplate">
  <ul>
    {{#arr}}
      <li>
        <div class="hd">{{name}}的基本信息</div>
        <div class="bd">
          <p>姓名：{{name}}</p>
          <p>性别：{{sex}}</p>
          <p>年龄：{{age}}</p>
        </div>
      </li>
    {{/arr}}
  </ul>
</script>

<script src="jslib/mustache.js"></script>
<script>
  var templateStr = document.getElementById("mytemplate").innerHTML;

  var data = {
    arr: [
      { name: "小明", age: 12, sex: "男" },
      { name: "小红", age: 11, sex: "女" },
      { name: "小强", age: 13, sex: "男" },
    ],
  };

  var domStr = Mustache.render(templateStr, data);
  var container = document.getElementById("container");
  container.innerHTML = domStr;
</script>
```

### 不循环

```html
<div id="container"></div>

<script src="jslib/mustache.js"></script>
<script>
  var templateStr = `<h1>我买了一个{{thing}}，好{{mood}}啊</h1>`;

  var data = {
    thing: "华为手机",
    mood: "开心",
  };

  var domStr = Mustache.render(templateStr, data);

  var container = document.getElementById("container");
  container.innerHTML = domStr;
</script>
```

### 循环简单数组

```html
<!-- ... -->
<script>
  var templateStr = `
    <ul>
        {{#arr}}
            <li>{{.}}</li>    
        {{/arr}}
    </ul>
`;

  var data = {
    arr: ["A", "B", "C"],
  };

  var domStr = Mustache.render(templateStr, data);

  var container = document.getElementById("container");
  container.innerHTML = domStr;
</script>
```

### 数组的嵌套情况

```html
<script>
  var templateStr = `
  <ul>
    {{#arr}}
      <li>
        {{name}}的爱好是：
        <ol>
          {{#hobbies}} 
            <li>{{.}}</li>
          {{/hobbies}} 
        </ol>
      </li>    
    {{/arr}}
  </ul>
`;
  var data = {
    arr: [
      { name: "小明", age: 12, hobbies: ["游泳", "羽毛球"] },
      { name: "小红", age: 11, hobbies: ["编程", "写作文", "看报纸"] },
      { name: "小强", age: 13, hobbies: ["打台球"] },
    ],
  };
  var domStr = Mustache.render(templateStr, data);
</script>
```

### 布尔值

```html
<script>
  var templateStr = `
            {{#m}}
                <h1>你好</h1>
            {{/m}}
        `;

  var data = {
    m: false,
  };

  var domStr = Mustache.render(templateStr, data);

  var container = document.getElementById("container");
  container.innerHTML = domStr;
</script>
```
