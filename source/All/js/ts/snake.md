---
title: 蛇模块
toc: true
date: 2021-11-03 16:33:24
---

```ts
//定义蛇类
class Snake {
  //表示蛇头的元素
  head: HTMLElement;
  //蛇的身体(包括蛇头)
  bodies: HTMLCollection;
  //蛇的容器
  element: HTMLElement;

  constructor() {
    this.element = document.getElementById("snake")!;
    this.head = document.querySelector("#snake > div") as HTMLElement;
    this.bodies = this.element.getElementsByTagName("div");
  }

  //获取蛇的X坐标(蛇头坐标)
  get X() {
    return this.head.offsetLeft;
  }
  //获取蛇的Y坐标(蛇头坐标)
  get Y() {
    return this.head.offsetTop;
  }

  set X(value: number) {
    //如果新值和旧值相同，则直接返回不修改
    if (this.X === value) return;

    //X的值的合法范围0-290之间
    if (value < 0 || value > 290) {
      //进入判断，说明蛇撞墙了
      throw new Error("蛇撞墙了");
    }

    //修改x时，是在修改水平坐标，蛇在左右移动，蛇在向左移动时，不能向右掉头，反之亦然
    if (
      this.bodies[1] &&
      (this.bodies[1] as HTMLElement).offsetLeft === value
    ) {
      //如果发生了掉了，让蛇向反方向继续移动
      if (value > this.X) {
        //如果新值vlue大于旧值X,则说明蛇在向右走，此时发生掉头，应该继续向左走
        value = this.X - 10;
      } else {
        //向左走，此时发生掉头，应该继续向右走
        value = this.X + 10;
      }
    }

    //移动身体
    this.moveBody();
    this.head.style.left = value + "px";

    //检查是否撞到自己
    this.checkHeadBody();
  }

  set Y(value: number) {
    //如果新值和旧值相同，则直接返回不修改
    if (this.Y === value) return;
    //Y的值的合法范围0-290之间
    if (value < 0 || value > 290) {
      //进入判断，说明蛇撞墙了
      throw new Error("蛇撞墙了");
    }

    //修改y时，是在修改垂直坐标，蛇在上下移动，蛇在向上移动时，不能向下掉头，反之亦然
    if (this.bodies[1] && (this.bodies[1] as HTMLElement).offsetTop === value) {
      //如果发生了掉头，让蛇向反方向继续移动
      if (value > this.Y) {
        //如果新值vlue大于旧值X,则说明蛇在向下走，此时发生掉头，应该继续向上走
        value = this.Y - 10;
      } else {
        //向上走，此时发生掉头，应该继续向下走
        value = this.Y + 10;
      }
    }

    //移动身体
    this.moveBody();
    this.head.style.top = value + "px";

    //检查是否撞到自己
    this.checkHeadBody();
  }

  //蛇增加身体的方法
  addBody() {
    //向element添加一个div
    this.element.insertAdjacentHTML("beforeend", "<div></div>");
  }

  //添加一个蛇身体移动的方法
  moveBody() {
    /**
     * 将后边的身体置为前边身体的位置
     *  第4节 = 第3节的位置
     *  第3节 = 第2节的职位
     *  第2节 = 蛇头的位置
     */
    //遍历获取所有的身体
    for (let i = this.bodies.length - 1; i > 0; i--) {
      //获取前边身体的位置
      let X = (this.bodies[i - 1] as HTMLElement).offsetLeft;
      let Y = (this.bodies[i - 1] as HTMLElement).offsetTop;
      //将这个值设置到当前身体上
      (this.bodies[i] as HTMLElement).style.left = X + "px";
      (this.bodies[i] as HTMLElement).style.top = Y + "px";
    }
  }

  //添加一个检查头和身体有没有相撞
  checkHeadBody() {
    //获取所有的身体，检查其是否和蛇头的坐标发生重叠
    for (let i = 1; i < this.bodies.length; i++) {
      let bd = this.bodies[i] as HTMLElement;
      if (this.X === bd.offsetLeft && this.Y === bd.offsetTop) {
        //进入判断，说明蛇头撞到身体，游戏结束
        throw new Error("撞到自己了!!");
      }
    }
  }
}

export default Snake;
```
