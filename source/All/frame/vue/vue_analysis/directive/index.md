---
title: 指令
date: 2022-01-27 19:03:24
toc: true
---

>这里我们简单实现一下，需要用到之前数据侦测用到的js文件

```html
<body>
  <div id="app">
    你好,计数器值为：{{b.m.n}}
    <br />
    <input type="text" v-model="b.m.n" />
    你好，a的值是:{{a}}
  </div>
  <button onclick="add()">按我加a+1</button>
  <script>
    let vm = new Vue({
      el: "#app",
      data: {
        a: 10,
        b: {
          m: {
            n: 7,
          },
        },
      },
      watch: {
        a() {
          console.log("监听a");
        },
      },
    });
    function add() {
      vm.a++;
    }
  </script>
</body>
```

**Vue.js**
```js
import observe from './observe';//observe函数
import Watcher from './Watcher';//监听器类
import Compile from './Compile';//编译类

export default class Vue {
  constructor(options = {}) {
    //将参数options存起啦
    this.$options = options;
    //数据
    this._data = options.data || undefined;
    //将数据变成响应式的
    observe(this._data);
    //代理，将options中data的数据代理到vue实例上
    this._initData();
    //调用默认的watch
    this._initWatch();
    //模板编译
    new Compile(options.el, this);
  }

  _initData() {
    //保存this指向
    let self = this;
    Object.keys(this._data).forEach(key => {
      Object.defineProperty(self, key, {
        get() {
          return self._data[key];
        },
        set(newVal) {
          return self._data[key] = newVal;
        }
      })
    })
  }

  _initWatch() {
    //保存this指向
    let self = this;
    let watch = this.$options.watch;
    Object.keys(watch).forEach(key => {
      new Watcher(self, key, watch[key]);
    })
  }
}
```

**Compile.js**
>将模板内容进行编译，解析model指令(这里只解析该指令)
```js
import Watcher from './Watcher';

export default class Compile {
  constructor(el, vue) {
    //vue实例
    this.$vue = vue;
    //挂载点
    this.$el = document.querySelector(el);
    //如果用户传入了挂载点
    if (this.$el) {
      //调用函数，让节点变为fragment,类似于mustache中的tokens。实际上用的是AST,这里就是轻量级的fragment.
      let $fragment = this.node2Fragment(this.$el);
      //编译
      this.compile($fragment);
      //编译完上树
      this.$el.appendChild($fragment);
    }
  }

  //将节点变成fragment
  node2Fragment(el) {
    //fragment 是一个指向空DocumentFragment对象的引用
    //DocumentFragments 是DOM节点。它们不是主DOM树的一部分。通常的用例是创建文档片段，将元素附加到文档片段，然后将文档片段附加到DOM树。在DOM树中，文档片段被其所有的子元素所代替。
    let fragment = document.createDocumentFragment();
    let child;
    //让所有DOM节点，都进入fragment
    while (child = el.firstChild) {
      fragment.appendChild(child);
    }
    return fragment;
  }

  //编译
  compile(el) {
    //得到子元素
    let childNodes = el.childNodes;
    let self = this;

    let reg = /\{\{(.*)\}\}/;

    childNodes.forEach(node => {
      let text = node.textContent;
      if (node.nodeType === 1) {
        //元素
        self.compileElement(node);
      } else if (node.nodeType === 3 && reg.test(text)) {
        //文本，且存在插值表达式
        let name = text.match(reg)[1];
        self.compileText(node, name, text, reg);
      }
    })
  }

  //处理文本,插值表达式里面的内容
  compileText(node, name, text, reg) {
    //name可能是a.b.c这样的形式，需要将他转换一下
    node.textContent = text.replace(reg, this.getVueVal(this.$vue, name));
    new Watcher(this.$vue, name, value => {
      node.textContent = text.replace(reg, value);
    });
  }

  //处理元素，指令
  compileElement(node) {
    // 这里的方便之处在于不是将HTML结构看做字符串，而是真正的属性列表
    let nodeAttrs = node.attributes;
    let self = this;

    //类数组对象变成数组
    Array.prototype.slice.call(nodeAttrs).forEach(attr => {
      //这里就分析指令
      let attrName = attr.name;
      let value = attr.value;

      //指令都是v-开头的
      if (attrName.indexOf('v-') === 0) {
        //v-开头的就是指令
        let dir = attrName.substring(2);
        if (dir === 'model') {
          console.log('发现了model指令');
          //添加监听器，当value值改变，重新设置值
          new Watcher(self.$vue, value, val => {
            node.value = val;
          });
          //初始时设置值
          let v = self.getVueVal(self.$vue, value);
          node.value = v;

          // 添加监听
          node.addEventListener('input', e => {
            var newVal = e.target.value;
            self.setVueVal(self.$vue, value, newVal);
            v = newVal;
          });
        }
      }
    })
  }

  //处理a.b.c这样的形式
  getVueVal(vue, exp) {
    let val = vue;
    exp = exp.split('.');
    exp.forEach(k => {
      val = val[k];
    })
    return val;
  }

  //设置值，当指令model输入时，需要改变原型上的值
  setVueVal(vue, exp, value) {
    let val = vue;
    exp = exp.split('.');
    //这里需要索引的原因是，当我们找到最后一个时，需要设置它的值
    exp.forEach((key, i) => {
      if (i < exp.length - 1) {
        val = val[key];
      } else {
        val[key] = value;
      }
    })
  }
}
```
