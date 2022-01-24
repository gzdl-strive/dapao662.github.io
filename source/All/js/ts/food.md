---
title: 食物模块
toc: true
date: 2021-11-03 16:33:24
---

```ts
//定义食物类-Food
class Food {
  //定义一个属性表示食物对应的元素
  element: HTMLElement;

  constructor() {
    //获取页面中的food元素并赋值给element
    //因为可能为null,后面加一个“!”表示一定不为空
    this.element = document.getElementById("food")!;
  }

  //定义一个获取食物X轴坐标的方法
  get X() {
    return this.element.offsetLeft;
  }
  //定义一个获取食物Y轴坐标的方法
  get Y() {
    return this.element.offsetTop;
  }

  //修改食物的位置
  change() {
    //生成一个随机的位置
    //食物的位置最小是0，最大是290
    //蛇移动一次就是一格，一格的大小就是，所以就要求食物的坐标必须是10的倍数
    let top = Math.round(Math.random() * 29) * 10;
    let left = Math.round(Math.random() * 29) * 10;

    this.element.style.left = left + "px";
    this.element.style.top = top + "px";
  }
}
```
