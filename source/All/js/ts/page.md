---
title: 静态页面
toc: true
date: 2021-11-03 16:25:02
---

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>贪吃蛇</title>
  </head>
  <body>
    <!-- 创建游戏的主容器 -->
    <div id="main">
      <!-- 设置游戏的舞台 -->
      <div id="stage">
        <div id="snake">
          <div></div>
        </div>

        <div id="food">
          <div></div>
          <div></div>
          <div></div>
          <div></div>
        </div>
      </div>
      <!-- 设置游戏记分牌 -->
      <div id="score-panel">
        <div>SCORE:<span id="score">0</span></div>
        <div>LEVEL:<span id="level">1</span></div>
      </div>
    </div>
  </body>
</html>
```

**src 目录下新建 style,style 新建 index.less 文件**

```less
//设置变量
@bg-color: #b7d4a8;

//清除默认样式
* {
  margin: 0;
  padding: 0;
  //改变盒模型的计算方式
  box-sizing: border-box;
}

body {
  font-size: 20px;
  font-weight: bold;
  font-family: "Courier";
}

// 设置主窗口的样式
#main {
  width: 360px;
  height: 420px;
  //居中
  margin: 100px auto;
  //背景色
  background-color: @bg-color;
  border: 10px solid black;
  //边框圆角
  border-radius: 10px;
  //开启弹性和模型
  display: flex;
  //主轴方向
  flex-direction: column;
  //侧轴对齐方式
  align-items: center;
  //主轴对齐方式
  justify-content: space-around;

  //游戏舞台
  #stage {
    width: 304px;
    height: 304px;
    border: 2px solid black;
    //开启相对定位
    position: relative;

    //蛇的样式
    #snake {
      & > div:nth-child(1) {
        background-color: #f00;
        position: relative;
      }
      & > div {
        width: 10px;
        height: 10px;
        background-color: #000;
        border: 1px solid @bg-color;
        //开启绝对定位
        position: absolute;
      }
    }

    //设置食物
    #food {
      width: 10px;
      height: 10px;
      position: absolute;
      left: 50px;
      top: 120px;
      //开启弹性盒子
      display: flex;
      //主轴方向和可以换行
      flex-flow: row wrap;
      justify-content: space-between;
      align-content: space-between;

      & > div {
        width: 4px;
        height: 4px;
        background-color: #000;
        transform: rotate(45deg);
      }
    }
  }

  //记分牌样式
  #score-panel {
    width: 300px;
    //开启弹性盒模型
    display: flex;
    //主轴对齐方式
    justify-content: space-between;
  }
}
```

**indexts中引入样式**
```ts
//引入样式
import './style/index.less';
```
