---
title: 渲染到页面上
date: 2022-01-08 21:54:24
toc: true
---

## index.js

整合前两个方法，暴露成一个全局的对象
```js
//index.js
import parseTemplateToTokens from './parseTemplateToTokens';
import renderTemplate from './renderTemplate';

//全局对象
window.My_TemplateEngine = {
  //渲染方法
  render(templateStr, data) {
    //调用parseTemplateToTokens函数，让模板字符串能够变成tokens数组
    let tokens = parseTemplateToTokens(templateStr);
    //根据tokens数组和data数据，生成dom字符串
    let domStr = renderTemplate(tokens, data);

    //返回dom字符串
    return domStr;
  }
}
```

## 使用
```html
<script src="./src/index.js"></script>
<div id="container"></div>
<script>
  const templateStr = `
        <div>
            <ul>
                {{#students}}
                <li class="myli">
                    学生{{name}}的爱好是
                    <ol>
                        {{#hobbies}}
                        <li>{{.}}</li>
                        {{/hobbies}}
                    </ol>
                </li>
                {{/students}}
            </ul>
        </div>
    `;

  var data = {
    students: [
      { name: "小明", hobbies: ["编程", "游泳"] },
      { name: "小红", hobbies: ["看书", "弹琴", "画画"] },
      { name: "小强", hobbies: ["锻炼"] },
    ],
  };

  let domStr = SSG_TemplateEngine.render(templateStr, data);

  //渲染出来
  let container = document.getElementById('container');
  container.innerHTML = domStr;
</script>
```
效果如下：
![render](/assets/vueImg/vueSource/mustache9.png "渲染结果")