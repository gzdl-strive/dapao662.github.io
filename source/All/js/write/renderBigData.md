---
title: 渲染10万条数据
date: 2022-08-09 09:48:24
toc: true
---

>
* 渲染大数据，使用createDocumentFragment和requestAnimationFrame,将操作切分成一小段一小段执行。
* createDocumentFragment方法创建了一虚拟的节点对象，节点对象包含所有属性和方法。
* createDocumentFragment创建的文档片段存在于内存中，所以将子元素插入到文档片段时不会引起页面回流(对元素位置和几何上的计算)
* 因此，使用文档片段通常会带来更好的性能

```js
setTimeout(() => {
  // 插入10万条数据
  const total = 100000;
  // 一次插入的数据
  const once = 20;
  // 插入数据需要的次数
  const loopCount = Math.ceil(total / once);
  let countOfRender = 0;
  const ul = document.querySelector('ul');

    // 添加数据的方法
  function add () {
    const fragment = document.createDocumentFragment();
    for (let i = 0; i < once; i++) {
      const li = document.createElement('li');
      li.innerText = `【${20 * (countOfRender + 1)}】- ${Math.floor(Math.random() * total)}` ;
      fragment.appendChild(li);
    }
    ul.appendChild(fragment);
    countOfRender += 1;
    loop();
  }

  function loop() {
    if (countOfRender < loopCount) {
      window.requestAnimationFrame(add);
    }
  }
  loop();
}, 0);
```