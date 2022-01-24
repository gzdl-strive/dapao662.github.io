---
title: 记分牌模块
toc: true
date: 2021-11-03 16:36:24
---

```ts
//定义表示记分牌的类
class ScorePanel {
  //score和level用来记录分数和等级
  score: number = 0;
  level: number = 1;
  scoreElement: HTMLElement;
  levelElement: HTMLElement;
  //设置一个变量限制等级
  maxLevel: number;
  //设置一个变量表示多少分时升级
  upScore: number;

  //分数和等级所在的元素，在构造函数中进行初始化
  constructor(maxLevel: number = 10, upScore: number = 10) {
    this.scoreElement = document.getElementById('score')!;
    this.levelElement = document.getElementById('level')!;
    this.maxLevel = maxLevel;
    this.upScore = upScore;
  }

  //设置一个加分的方法
  addScore() {
    //使分数自增
    this.scoreElement.innerHTML = ++this.score + '';
    //判断分数是多少
    if (this.score % this.upScore === 0) {
      //每十分升一级
      this.levelUp();
    }
  }

  //设置一个提示等级的方法
  levelUp() {
    if (this.level < this.maxLevel) {
      this.levelElement.innerHTML = ++this.level + '';
    }
  }
}

export default ScorePanel;
```