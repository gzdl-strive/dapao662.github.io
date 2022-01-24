---
title: 控制器模块
toc: true
date: 2021-11-03 16:37:24
---

```ts
//引入其他类
import Snake from "./Snake";
import Food from "./Food";
import ScorePanel from "./ScorePanel";

//游戏控制器，控制其他所有类
class GameControl {
  //定义三个属性
  snake: Snake;
  food: Food;
  scorePanel: ScorePanel;

  //创建一个属性来存储蛇的移动方向
  direction: string = "";
  //创建一个属性用来记录游戏是否结束
  isLive: boolean = true;

  constructor() {
    this.snake = new Snake();
    this.food = new Food();
    this.scorePanel = new ScorePanel(10, 2);

    this.init();
  }

  //游戏初始化方法，调用后游戏即开始
  init() {
    //表单键盘按键按下的事件
    document.addEventListener("keydown", this.keydownHandler.bind(this));
    this.run();
  }

  //创建一个键盘按下的响应函数
  /** chrome ie
   * ArrowUp Up
   * ArrowDown Down
   * ArrowLeft Left
   * ArrowRight Right
   */
  keydownHandler(event: KeyboardEvent) {
    //需要检查event.key的值是否合法(用户是否按了正确的按键)
    //修改direction属性
    if (event.keyCode >= 37 && event.keyCode <= 40) {
      this.direction = event.key;
    }
  }

  //创建一个控制蛇移动的方法
  run() {
    //根据方向this.direction来使蛇的位置移动
    /*
      向上 top减小
      向下 top增加
      向左 left减小
      向右 left增加
    */
    let X = this.snake.X;
    let Y = this.snake.Y;

    //根据按键的方向来修改X和Y值（局部变量）
    switch (this.direction) {
      case "ArrowUp":
      case "Up":
        //向上移动 top减小
        Y -= 10;
        break;
      case "ArrowDown":
      case "Down":
        Y += 10;
        break;
      case "ArrowLeft":
      case "Left":
        X -= 10;
        break;
      case "ArrowRight":
      case "Right":
        X += 10;
        break;
    }

    this.checkEat(X, Y);

    //修改蛇的X和Y值
    try {
      this.snake.X = X;
      this.snake.Y = Y;
    } catch (e: unknown) {
      const err = e as any;
      alert(err.message + "GAME OVER!");
      this.isLive = false;
    }

    //开启一个定时调用
    this.isLive &&
      setTimeout(this.run.bind(this), 300 - (this.scorePanel.level - 1) * 30);
  }

  //定义一个方法，用来检查蛇是否迟到食物
  checkEat(X: number, Y: number) {
    if (X === this.food.X && Y === this.food.Y) {
      //吃到食物
      //食物的位置要进行重置
      this.food.change();
      //分数增加
      this.scorePanel.addScore();
      //蛇要增加一节
      this.snake.addBody();
    }
  }
}

export default GameControl;
```
