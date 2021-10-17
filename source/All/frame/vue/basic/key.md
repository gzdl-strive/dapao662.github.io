---
title: key的原理与模拟数据监测
date: 2021-10-13 16:40:24
toc: true
---

### 面试题：react、vue中的key有什么作用?(key的内部原理)
1. 虚拟DOM中key的作用：
  key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】。
  随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较(diff),比较规则如下：
2. 对比规则：
  (1)、旧虚拟DOM中找到了与新虚拟DOM相同的key:
    <1>. 若虚拟DOM中内容没变，直接使用之前的真实DOM
    <2>. 若虚拟DOM中内容变了，则生成新的真实DOM,随后替换掉页面中之前的真实DOM
  (2)、旧虚拟DOM中未找到与新虚拟DOM相同的key:
      创建新的真实DOM，随后渲染到页面中
3、index作为key可能引发的问题：
  (1)、若对数据进行：逆序添加、逆序删除等破坏顺序操作：
      会产生没有必要的真实DOM更新 ==> 界面效果没问题，但效率低
  (2)、如果结构中还包含输入类的DOM:
      会产生错误DOM更新 ==> 界面有问题
4、开发中如何选择key?:
  (1)、最好使用每条数据的唯一标识作为Key,比如id,手机号，身份证号，学号等唯一值
  (2)、如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用index作为key也是没有问题的

### 模拟数据监测
```html
  <script>
    let data = {
      name: 's1mple',
      address: '乌克兰',
      alias: '剑圣'
    };

    //创建一个监视的实例对象,用于监视data中属性的变化
    const obs = new Observer(data);
    console.log(obs);

    //准备一个vm实例对象
    let vm = {};
    vm._data = data = obs;

    //Observer: 观察者
    function Observer(obj) {
      //收集对象中所有属性形成一个数组
      const keys = Object.keys(obj);
      //遍历
      keys.forEach((k) => {
        //this指向这个构造函数的实例对象
        Object.defineProperty(this, k, {
          get() {
            return obj[k];
          },
          set(val) {
            console.log(`${k}被修改了...`);
            obj[k] = val;
          }
        })
      })
    }
  </script>
```

### 总结数据监测
Vue监视数据的原理：
1. vue会监视data中所有层次的数据。

2. 如何监测对象中的数据？
  通过setter实现监视，且要在new Vue时就传入要监测的数据。
    (1).对象中后追加的属性，Vue默认不做响应式处理
    (2).如需给后添加的属性做响应式，请使用如下API：
      `Vue.set(target，propertyName/index，value)` 或 `vm.$set(target，propertyName/index，value)`

3. 如何监测数组中的数据？
  通过包裹数组更新元素的方法实现，本质就是做了两件事：
    (1).调用原生对应的方法对数组进行更新。
    (2).重新解析模板，进而更新页面。

4. 在Vue修改数组中的某个元素一定要用如下方法：
  1.使用这些API:push()、pop()、shift()、unshift()、splice()、sort()、reverse()
  2.Vue.set() 或 vm.$set()

特别注意：Vue.set() 和 vm.$set() 不能给vm 或 vm的根数据对象 添加属性！！！