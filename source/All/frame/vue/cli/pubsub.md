---
title: 消息订阅与发布
date: 2021-10-14 14:35:24
toc: true
---

## 消息订阅与发布(pubsub)
1. 一种组件间通信的方式，适用于<span style="color:red">任意组件间通信</span>。

2. 使用步骤：
   1. 安装pubsub：```npm i pubsub-js```
   2. 引入: ```import pubsub from 'pubsub-js'```
   3. 接收数据：A组件想接收数据，则在A组件中订阅消息，订阅的<span style="color:red">回调留在A组件自身。</span>
      ```js
      methods(){
        demo(data){......}
      }
      ......
      mounted() {
        this.pid = pubsub.subscribe('xxx',this.demo) //订阅消息
      }
      ```
   4. 提供数据：```pubsub.publish('xxx',数据)```
   5. 最好在beforeDestroy钩子中，用```PubSub.unsubscribe(pid)```去<span style="color:red">取消订阅。</span>

## 例子
B组件是A组件的子组件，B组件向A组件传参

**A组件**
A组件想要接收数据，需要订阅消息，等B组件消息发布的时候接收，回调留在自身
```js
import pubsub from 'pubsub-js';
export default {
  //挂载后就订阅
  mounted() {
    this.pubId = pubsub.subscribe('info', () => {
      //...
    })
  },
  //销毁该组件之前 取消之前订阅过的消息
  beforeDestroy() {
    pubsub.unsubscribe(this.pubId);
  }
}
```

**B组件**
B组件发送数据，需要发布消息
```js
import pubsub from 'pubsub-js';
export default {
  methods: {
    //事件的回调
    publishInfo() {
      pubsub.puslish('info', ....);
    }
  }
}
```