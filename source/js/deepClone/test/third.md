---
title: 进一步优化
date: 2020-11-15 20:41:02
tag: js
---
到目前为止，我们虽然写出了一个可使用的深拷贝函数，但是这个函数仍然存在着许多可优化的地方。（这些优化的地方也是面试官容易问到的地方）。
## 3.1忽略原型上的属性
我们在遍历对象的属性的时候，使用的是`for in`，`for in `会遍历包括原型上的所有可迭代的属性。 比如：
```js
let a = Object.create({name:'hello'});
a.age = 14;
```
那么使用遍历时，会遍历`name`和`age`属性。而不仅仅是`a`自身身上的`age`属性。但是，事实上我们不应该去遍历原型上的属性，因为这样会导致对象属性非常深。因此，使用`for in`遍历时我们最好把原型上的属性和自身属性区分开来，通过`hasOwnProperty`筛选出自身的属性进行遍历。
```js
for (let key in source) {
    // 只遍历本身的属性
    if(source.hasOwnProperty(key)){
        dist[key] = deepClone(source[key]);
    }
}
```
因此，优化后的代码如下：
```js
function deepClone(target){
  if(target instanceof Object){
      let dist ;
      if(target instanceof Array){
        // 拷贝数组
        dist = [];
      }else if(target instanceof Function){
        // 拷贝函数
        dist = function () {
          return target.call(this, ...arguments);
        };
      }else if(target instanceof RegExp){
        // 拷贝正则表达式
       dist = new RegExp(target.source,target.flags);
      }else if(target instanceof Date){
          dist = new Date(target);
      }else{
        // 拷贝普通对象
        dist = {};
      }
      for(let key in target){
          // 过滤掉原型身上的属性
        if (target.hasOwnProperty(key)) {
            dist[key] = deepClone(target[key]);
        }
      }
      return dist;
  }else{
      return target;
  }
}
```
## 3.2 环状对象的爆栈问题
我们在之前使用`JSON.parse(JSON.stringify())`拷贝对象时，就遇到过如果出现环状对象，会导致报错问题。那么使用我们自己的深拷贝函数同样会遇到问题。这是由于我们在`deepClone`函数中使用了递归，按理来说每一个递归应该有一个终止条件的，但是由于对象树结构一般会有终点，因此会自动在终点结束递归。但是如果一个对象有属性指向自身，那么就会形成一个环，比如：
```js
let a = {name:"小明"};
a.self = a;   // a的self属性指向a
```
这样的话，在进行递归调用的过程中会无限循环，最终爆栈。因此，我们需要添加递归终止条件。所谓的递归终止条件，就是判断一个对象是否已经被克隆过了，如果被克隆过了那么就直接使用克隆后的对象，不再进行递归。因此，我们需要一个东西来保存可能重复的属性以及它的克隆地址。最好的方式就是`Map`。
![1](/assets/jsImg/deepCloneImg/3-1.jpg)
这里大家可能有点难以理解，因此我们用更加直观的图形方式来介绍：
上图中我们依次拷贝属性a，属性b和属性c对应的拷贝后的属性为a1,b1和c1。其中属性c又指向了属性a，因此拷贝时我们又得拷贝一次属性a，这样的话就不断地形成循环，最终递归导致爆栈。因此，对于a这种已经拷贝过的属性，我们可以使用一个东西把它和它对应的拷贝对象地址保存起来，如果遇到c这种又指向a的，只需要把保存的对象地址赋值给c即可。这种需要两个值，而且一一对应最常见的数据结构就是object或者Map。当然使用数组也行。这里我们使用map来进行保存。
```js
let cache = new Map();              /*新增代码*/
function deepClone(target){
  if(cache.get(target)){            /*新增代码*/
      return cache.get(target)
  }
  if(target instanceof Object){
      let dist ;
      if(target instanceof Array){
        // 拷贝数组
        dist = [];
      }else if(target instanceof Function){
        // 拷贝函数
        dist = function () {
          return target.call(this, ...arguments);
        };
      }else if(target instanceof RegExp){
        // 拷贝正则表达式
       dist = new RegExp(target.source,target.flags);
      }else if(target instanceof Date){
          dist = new Date(target);
      }else{
        // 拷贝普通对象
        dist = {};
      }
      // 将属性和拷贝后的值作为一个Map
      cache.set(target, dist);          /*新增代码*/
      for(let key in target){
          // 过滤掉原型身上的属性
        if (target.hasOwnProperty(key)) {
            dist[key] = deepClone(target[key]);
        }
      }
      return dist;
  }else{
      return target;
  }
}
```

## 3.3共用缓存导致的互相影响问题
在上面的`deepClone`函数中，我们通过新增了一个缓存`cache`来保存已经克隆过的对象和它对应的克隆地址。但是这种方式会带来一个新的问题：由于每次克隆创建一个对象都会使用这个`cache`，这样的话会导致克隆一个新的对象受到上一个克隆对象的影响。示例：
```js
let a = {
name:"hello",
}     
let a1 = deepClone(a);
console.log(cache);  //{ name: 'hello' } => { name: 'hello' }
let b = {
age:24
}
let b1 = deepClone(b);
console.log(cache);  //   { name: 'hello' } => { name: 'hello' },{ age: 24 } => { age: 24 } }
```
我们发现在深拷贝对象b的时候，`Map`中已经有值了`{ name: 'hello' }`.而事实上这些值不是b身上已经拷贝过的属性。也就是说b的拷贝受到了a的拷贝的影响，这会导致问题。因此，我们不能让所有的深拷贝共用同一个缓存，而是让每一个深拷贝使用自己的属性。
**解决办法是：在调用函数时，每次都创建一个新的map（默认参数），然后如果需要递归，就把这个map往下传。**
```js
function deepClone(target,cache = new Map()){               /*新增代码*/
  if(cache.get(target)){
      return cache.get(target)
  }
  if(target instanceof Object){
      let dist ;
      if(target instanceof Array){
        // 拷贝数组
        dist = [];
      }else if(target instanceof Function){
        // 拷贝函数
        dist = function () {
          return target.call(this, ...arguments);
        };
      }else if(target instanceof RegExp){
        // 拷贝正则表达式
       dist = new RegExp(target.source,target.flags);
      }else if(target instanceof Date){
          dist = new Date(target);
      }else{
        // 拷贝普通对象
        dist = {};
      }
      // 将属性和拷贝后的值作为一个map
      cache.set(target, dist);
      for(let key in target){
          // 过滤掉原型身上的属性
        if (target.hasOwnProperty(key)) {
            dist[key] = deepClone(target[key], cache);
        }
      }
      return dist;
  }else{
      return target;
  }
}
```
## 3.4对象过长导致的爆栈问题
我们知道我们深拷贝中使用了递归，而递归是有递归栈的，递归栈的深度是有限的，一旦对象的递归深度超过了递归栈的深度，那么就可能出现爆栈。 比如，下面的对象a的对象深度有`20000`个属性。这样的话基本上递归到`5000`时就出现爆栈了，导致报错。
```js
let a = {
child:null 
}
let b = a;
for(let i = 0;i < 20;i++){
b.child = {
    child:null
}
b = b.child;
}
console.log(a);
```
这种由于对象过深导致的爆栈问题，暂时没有什么解决办法，而且也很少会有这么深的对象。


作者：海因斯坦
链接：https://juejin.im/post/6889327058158092302
来源：掘金
