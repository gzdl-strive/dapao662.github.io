---
title: 事件/修饰符/键盘事件
date: 2021-10-13 15:49:24
toc: true
---

## 事件的基本使用
  1. 使用v-on:xxx 或 @xxx 绑定事件，其中xxx是事件名称
  2. 事件的回调需要配置在methods对象中，最终会出现在vm上
  3. methods中配置的函数，不要用箭头函数，否则this就不是vm了
  4. methods中配置的函数，都是被Vue所管理的函数，this的指向是vm 或 组件实例对象
  5. @click="demo" 和 @click="demo($event)"效果一致，但后者可以传参
```html
  <div id="app">
    <h2>{{ name }}</h2>
    <button v-on:click="showInfo">点击</button>
    <button @click="showInfo2(66)">点击2</button>
    <button @click="showInfo3($event, 99)">点击3</button>
  </div>
  <script>
    Vue.config.productionTip = false;
    const vm = new Vue({
      data() {
        return {
          name: 's1mple',
        }
      },
      methods: {
        //默认传参是event对象
        showInfo(event) {
          console.log(event.target.innerText)
          // console.log(this === vm) //true
          // alert('Hello Major');
        },
        //假如参数就会是你要传的参数
        showInfo2(number) {
          console.log(number);//66
        },
        //但是此时我们即想要传参，也要event, 使用关键字$event
        showInfo3(event, number) {
          console.log(event.target.innerText);//点击3
          console.log(number);//99
        }
      }
    });
    vm.$mount('#app');
  </script>
```

## 事件修饰符
Vue中的事件修饰符：
  1. .prevent: 阻止默认事件(常用)
  2. .stop：阻止事件冒泡(常用)
  3. .once: 事件只触发一次（常用）
  4. .capture: 使用事件的捕获模式
  5. .self: 只有event.target是当前操作的元素时才触发事件
  6. .passive: 事件的默认行为立即执行，无需等待事件回调执行完毕
修饰符可以串联
`<a v-on:click.stop.prevent="doThat"></a>`

```html
<div id="app">
  <h1>{{ name }}</h1>
  <!-- 阻止默认事件(常用 xxx.prevent xxx事件名称) -->
  <a href="http://www.baidu.com" @click.prevent="showInfo">百度</a>
  <!-- 阻止事件冒泡 -->
  <div class="demo1" @click="showInfo">
    <button @click.stop="showInfo">点击</button>
  </div>
  <!-- 事件只触发一次 -->
  <button @click.once="showInfo">once</button>

  <!-- 使用事件捕获 -->
  <div class="demo1" @click.capture="showInfo2('parent')">
    <button @click="showInfo2('child')">capture</button>
  </div>

  <!-- 只有event.target是当前操作的元素时才触发事件 -->
  <div class="demo1" @click.self="showMsg">
    <button @click="showMsg">self</button>
  </div>

  <!-- .passive: 事件的默认行为立即执行 (滚动条触发事件wheal, scroll) -->
</div>
<script>
  new Vue({
    ...,
    methods: {
      showInfo(e) {
        // e.preventDefault(); //阻止默认事件
        // e.stopPropagation(); //阻止事件冒泡
        alert(`Hello ${this.name}`);
      },
    }
  })
</script>
```

## 键盘事件
1. Vue中常用的按键别名
  回车 => enter  删除 => delete(捕获"删除"和"退格"键)
  退出 => esc   空格space
  换行 => tab(配合keydown事件使用)   上 => up 下 => down 左 => left 右 => right
2. Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为kebab-case（短横线命名）
  例如： CapsLock => caps-lock
3. 系统修饰键(用法特殊): ctrl alt shift meta(win)
  (1).配合keyup使用：按下修饰键的同时，在按下其他键，随后释放其他键，事件才被触发
    也可以连写(.ctrl.y)
  (2). 配合keydown使用，正常触发事件
4. 也可以适用于keyCode去指定具体的按键(不推荐)
5. Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名
```html
  <div id="app">
    <h2>{{ name }}</h2>
    <input type="text" placeholder="按下回车输出" @keyup.enter="showInfo">
  </div>
  <script>
    Vue.config.productionTip = false;
    Vue.config.keyCodes.huiche = 13;
    const vm = new Vue({
      data() {
        return {
          name: 's1mple',
        }
      },
      methods: {
        showInfo(e) {
          console.log(e.target.value);
        }
      }
    }).$mount('#app');
  </script>
```