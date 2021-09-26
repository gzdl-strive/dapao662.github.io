---
title: 简易Vue实现
date: 2021-09-13 22:03:24
toc: true
---

实现Vue的思路：
>- 1、把传入的data做数据响应式处理
>- 2、编译模板

### data数据响应式处理
```js
class KVue() {
  constructor(options) {
    this.$options = options;
    this.$data = options.data;
  }

  //data响应式处理
  observe(this.$data);

  //编译
}
```

数据响应式可以参考[数据响应式](/All/frame/vue/write/responsive "响应式")内容
**数据响应式**
```js
function defineReactive(obj, key, val) {
  //递归，处理嵌套
  observe(val);

  Object.defineProperty(obj, key, {
    get() {
      console.log('get', key);
      return val;//生成闭包
    },
    set(newVal) {
      if (newVal !== val) {
        console.log('set', key);
        //保证：如果newVal是对象，再次做响应式处理
        observe(newVal);
        val = newVal;
      }
    }
  })
}

//遍历obj,对所有属性做响应式
function observe(obj) {
  if (typeof obj !== 'object' || obj === null) {
    return;
  }
  new Observer(obj);//遍历所有Key,做响应式处理
}
//根据传入value的类型做相应的响应式处理
class Observer {
  constructor(value) {
    this.value = value;

    if (Array.isArray(value)) {
      //todo
    } else {
      this.walk(value);
    }
  }
  //对象响应式
  walk(obj) {
    Object.keys(obj).forEach(key => defineReactive(obj, key, obj[key]))
  }
}
```

### 代理
>我们访问KVue实例时，想要直接访问到data中的数据，所以需要做一层代理
**给vue实例上添加`vue.$data`上的属性，这样用户就可以直接从实例上访问到$data的属性**

```js
//代理
function proxy(vm) {
  //遍历$data给vue实例添加属性
  Object.keys(vm.$data).forEach(key => {
    Object.defineProperty(vm, key, {
      get() {
        return vm.$data[key];
      },
      set(newValue) {
        vm.$data[key] = newValue;
      }
    })
  })
}

class KVue() {
  constructor(options) {
    this.$options = options;
    this.$data = options.data;

    //1.响应式处理
    observe(this.$data);

    //2.代理
    proxy(this);//给当前实例做代理

    //3.编译
  }
}
```

### 编译(解析模板)
>- 1、处理插值表达式
>- 2、处理指令和事件
>- 3、以上两者初始化和更新

```js
class Compile {
  //接受两个初始化值，el为需要编译的dom节点id,vm为实例
  constructor(el, vm) {
    this.$vm = vm;
    this.$el = document.querySelector(el);

    if (this.$el) {
      this.compile(this.$el);
    }
  }

  compile(el) {
    //遍历el子节点，根据他们的类型做相应的处理
    const childNodes = el.childNodes;

    childNodes.forEach(node => {
      if (node.nodeType === 1) {
        //元素
        //判断属性中是否存在有指令或事件
        //处理指令和事件
        const attrs = node.attributes;
        Array.from(attrs).forEach(attr => {
          // k-text="abc"
          const attrName = attr.name;//k-text
          const exp = attr.value;//abc
          //指令
          if (attrName.startsWith('k-')) {
            const dir = attrName.substring(2);
            this[dir] && this[dir](node, exp);
          }
          //事件
          if (attrName.startsWith('@')) {
            const dir = attrName.substring(1);
            this.eventHandler(node, exp, dir);
          }
        })
      } else if (this.isInter(node)) {
        //文本,且含有插值表达式
        this.compileText(node);
      }
    })
  }

  //编译文本
  compileText(node) {
    // console.log(RegExp.$1); //counter
    //this.$vm可以访问到data的原因是因为我们之前已经做了代理
    node.textContent = this.$vm[RegExp.$1];
  }

  //k-text指令
  text(node, exp) {
    node.textContent = this.$vm[exp];
  }

  //k-html指令
  html(node, exp) {
    node.innerHTML = this.$vm[exp];
  }

  //事件处理函数(节点、值、事件名称)
  eventHandler(node, exp, dir) {
    const fn = this.$vm.$options.methods && this.$vm.$options.methods[exp];
    //bind的原因是 事件执行函数内部可能会用到this
    node.addEventListener(dir, fn.bind(this.$vm));
  }

  //是否插值表达式
  isInter(node) {
    return node.nodeType === 3 && /\{\{(.*)\}\}/.test(node.textContent);
  }
}
```

**现在，我们已经能够看到页面的改变，但是还没有更新逻辑，也就是说我们的页面不能更新**

### 更新
>- 1、defineReactive时为每一个key创建一个Dep实例
>- 2、初始化视图时读取某个key,例如name1,创建一个watcher1
>- 3、由于触发name1的getter方法，便将watcher1添加到name1对应的Dep中
>- 4、当name1更新，setter触发时，便可通过对应Dep通知其管理所有watcher更新(观察者模式)

```js
//监听器：赋值依赖的更新
class Watcher {
  constructor(vm, key, updateFn) {
    this.vm = vm;
    this.key = key;
    this.updateFn = updateFn;

    //触发依赖收集
    Dep.target = this;
    this.vm[this.key];//触发get(get里面进行依赖收集)
    Dep.target = null;
  }

  update() {
    //执行实际更新操作
    this.updateFn.call(this.vm, this.vm[this.key])
  }
}
class Dep {
  constructor() {
    this.deps = [];
  }

  addDep(dep) {
    this.deps.push(dep);
  }
  notify() {
    //dep里面放的是一个个监听器
    this.deps.forEach(dep => dep.update())
  }
}
```
**上面两个类就是我们的监听器类和依赖类**
现在我们需要修改之前的代码
```js
//class Compile，我们现在只能初始化，不能更新，而能动态更新的方法就是插值表达式中的和指令
class Compile {
  //...

  //我们新建一个update函数，用于处理初始化和更新
  update() {
    //1、初始化
    const fn = this[dir + 'Updater'];
    fn && fn(node, this.$vm[exp]);
    //更新
    //三个参数：实例，key,更新函数
    new Watcher(this.$vm, exp, function(val) {
      //val是最小的值 -》 set传入的
      fn && fn(node, val);
    })
  }

  //修改compileText, text,html
  compileText(node) {
    //调用update
    this.update(node, RegExp.$1, 'text'); 
  }
  text(node, exp) {
    this.update(node, exp, 'text')
  }
  textUpdater(node, val) {
    node.textContent = val;
  }
  html(node, exp) {
    this.update(node, exp, 'html')
  }
  htmlUpdater(node, val) {
    node.innserHTML = val;
  }
}
```
**接着处理defineReactive**
```js
function defineReactive(obj, key, val) {
  //递归，处理嵌套
  observe(val);
  //创建Dep实例
  const dep = new Dep();
  Object.defineProperty(obj, key, {
    get() {
      console.log('get', key);
      //依赖收集
      Dep.target && dep.addDep(Dep.target);
      return val;
    },
    set(newVal) {
      if (newVal !== val) {
        console.log('set', key);
        observe(newVal);
        val = newVal;

        //触发依赖
        dep.notify();
      }
    }
  })
}
```

### 新增k-model方法
```js
class Compile{
  //...

  model(node, exp) {
    //update方法只完成赋值和更新
    this.update(node, exp, 'model');
    //我们还需要监听事件
    node.addEventListener('input', e => {
      this.$vm[exp] = e.target.value;
    })
  }

  modelUpdater(node, value) {
    node.value = value;
  }
}
```

