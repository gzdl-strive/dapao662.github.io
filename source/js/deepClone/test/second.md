---
title: 深拷贝的实现
date: 2020-11-15 20:07:02
tag: js
toc: true
---

## 2.1序列化与反序列化
在上面的深拷贝的代码示例中，我使用了`JSON.parse(JSON.stringify)`实现了一个深拷贝。这就是日常开发中使用较为频繁的一个深拷贝方法，它可以实现一些不是那么复杂的数据类型的深拷贝。示例：
```js
let num = 24;
let bool = true;
let obj = {
  id:1
  info:{
    name:"hello",
    age:24
  }
}

let num1 = JSON.parse(JSON.stringify(num))// num1就是num的深拷贝   虽然简单的数据类型这种拷贝没啥意义
let bool1 = JSON.parse(JSON.stringify(bool))// num1就是num的深拷贝   虽然简单的数据类型这种拷贝没啥意义
let obj2 = JSON.parse(JSON.stringify(obj))// 复杂数据类型也可以使用JSON.parse(JSON.stringify(obj))
```
但是这种方法存在一些缺点，由于它是依赖于`JSON`，因此它不支持`JSON`不支持的其他格式，通过`JSON`的官网可知，`JSON`只支持`object,array,string,number,true,false,null`这几种数据或者值，其他的比如`函数，undefined，Date，RegExp`等数据类型都不支持。对于它不支持的数据都会直接忽略该属性。
**1. 对象中不能有函数，否则无法序列化**
![1](/assets/jsImg/deepCloneImg/2-1.jpg "对象中存在函数")
**2. 对象中不能有undefined，否则无法序列化**
![2](/assets/jsImg/deepCloneImg/2-2.jpg "对象中存在undefined")
**3. 对象中不能有RegExp正则，否则无法序列化**
![3](/assets/jsImg/deepCloneImg/2-3.jpg "对象中存在RegExp")
**4. Date类型数据会被转化为字符串类型**
![4](/assets/jsImg/deepCloneImg/2-4.jpg "对象中存在Date")
**5. 对象不能是环状结构的，否则会导致报错**
所谓环状结构的对象，就是对象的属性又指向了自身，`window`就是最常见的一个环状对象。
小结：从上面的分析中，我们可以看到`JSON.parse(JSON.stringify())`虽然能够深拷贝一个对象，但是存在很大的局限性，对于复杂的对象就不适用了。因此，我们需要采用另外的方式来实现深拷贝，也就是通过递归的方式手动实现深拷贝。

## 2.2递归克隆
我们在第一部分讲述了数据的基本类型，任何的数据都时由这些类型组成的，只是因为这些类型的差异比如简单类型和复杂类型`(Object)`，复杂类型的子类型`(Array,Function,Date)`之间的差异导致了深拷贝的各种问题。
因此，我们只需要实现依次下面这些数据类型的拷贝，就能够很好地实现所有数据的深拷贝了。

![5](/assets/jsImg/deepCloneImg/1-1.jpg "数据类型")
### 2.2.1拷贝简单数据类型
如果是简单的数据类型，由于保存的是值，因此只需要返回这个值就行，不存在相互影响的问题。实现如下：
```js
function deepClone(target) {
    return target;
}
```
### 2.2.2拷贝简单的对象
所谓简单的对象，是指这些对象是由上面的简单数据类型组成的，不存在Array,Function,Date等子类型的数据。比如这种：
```js
let obj1 = {
  name:"hello",
  child:{
    name:"小明"
  }
}
```
实现思路就是创建一个新的对象，然后把每个对象上的属性拷贝到新对象上。如果这个属性是简单类型的那么就直接返回这个属性值。如果是`Object`类型，那么就通过`for...in`遍历讲对象上的每个属性一个一个地添加到新的对象身上。因为无法区分对象的层级，因此使用递归，每次赋值时都是调用自己，反正如果时简单类型就递归一次直接返回值，如果是`Object`类型，那么就往下递归查找赋值。
```js
function deepClone(target) {
    if (target instanceof Object) {
        let dist = {};
        for(let key in target) {
            //递归调用自己获取到每个值
            dist[key] = deepClone(target[key]);
        }
        return dist;
    } else {
        return target;
    }
}
```
我们使用上面的深拷贝函数，进行简单的测试。拷贝简单的对象，拷贝后对象中所有的引用类型必须是不相同的，但是所有的简单数据类型的值是相同的（但是他们其实不是同一个），比如：
```js
let obj1 = {
  name:"hello",
  child:{
    name:"小明"
  }
}
let obj2 = deepClone(obj1);
console.log(obj2 !== obj1);                  // true
console.log(obj2.name === obj1.name);        // true
console.log(obj2.child !== obj1.child);      // true
console.log(obj2.child.name === obj1.child.name); // true
obj2.name = "World";
console.log(obj1.name === 'hello');          // true
```
### 2.2.3拷贝复杂对象--数组
使用上面的方法我们能够实现拷贝简单的对象，但是对于一些包含子类型的对象，比如数组无法实现。我们看下代码：
```js
const a = [[11,12],[21,22]];
const a2 = deepClone(a);
console.log('........:',a2); //{ '0': { '0': 11, '1': 12 }, '1': { '0': 21, '1': 22 } }
```
我们发现拷贝后的数组，得到的是一个特殊的对象。这个对象以数组的下标作为`key`值，数组的每一项作为`value`值，这是因为`for in `在遍历数组时由于找不到`key`值会默认以数组的下标作为`key`值，数组的每一项作为`value`值。这样的话最终克隆后得到的数据类型就跟数组不一致了（实际上这就是数组本身的特殊造成的）。最终由数组拷贝后变成了对象。
我们发现问题出在我们把所有的东西都定义成一个{}了，而数组是不能用{}来描述的，因此我们需要根据对象的类型来区分一下最终返回的数据类型。实现代码如下：
```js
// 先不优化代码
function deepClone(target){
  if(target instanceof Object){
      let dist ;
      if(target instanceof Array){
        // 如果是数组，就创建一个[]
        dist = []
      }else{
        dist = {};
      }
      for(let key in target){
          dist[key] = deepClone(target[key]);
      }
      return dist;
  }else{
      return target;
  }
}
```
**由于数组也可以通过`for in`进行遍历，因此实际上我们要修改的就是在克隆时，先判断要克隆的对象是不是数组即可。**

### 2.2.4拷贝复杂对象--函数
拷贝函数这个其实有点争议，因为在很多人看来函数是无法拷贝的。在我看来函数实际上不应该有深拷贝的，如果真的要有，那么也就是实现函数的功能，同时函数的对象也必须是符合深拷贝的逻辑(引用属性不等，简单类型属性相等)：
1. 函数实现的功能要相同——返回的值相同
2. 函数身上的引用类型的属性要不相同，直接类型的属性的值要相同。
如下代码所示：
```js
const fn = function(){return 1};
fn.xxx = {yyy:{zzz:1}};
const fn2 = deepClone(fn);
console.log(fn !== fn2);             // 函数不相同
console.log(fn.xxx!== fn2.xxx);      // 函数引用类型的属性不相同
console.log(fn.xxx.yyy!== fn2.xxx.yyy);  // 函数引用类型的属性不相同
console.log(fn.xxx.yyy.zzz === fn2.xxx.yyy.zzz);// 函数简单类型的属性值相同
console.log(fn() === fn2());        //  函数执行后相等
```
那么应该如何实现一个函数的拷贝了？
1. 首先需要返回一个新的函数
2. 新的函数执行结果必须与原函数相同。
```js
function deepClone(target){
  if(target instanceof Object){
      let dist ;
      if(target instanceof Array){
        dist = []
      }else if(target instanceof Function){
        dist = function(){
            // 在函数中去执行原来的函数，确保返回的值相同
            return target.call(this, ...arguments);
        }
      }else{
        dist = {};
      }
      for(let key in target){
          dist[key] = deepClone(target[key]);
      }
      return dist;
  }else{
      return target;
  }
}
```
### 2.2.5拷贝复杂对象--正则表达式
如何拷贝一个正则了？以一个简单的正则为例：
```js
const a = /hi\d/ig;
```
一个正则，其实由两部分组成，正则的模式`patten`（斜杠之间的内容）hi\d,以及参数`flag`(ig)。因此，只要能够拿到这两部分就可以得到一个正则表达式。从而实现克隆这个正则。通过正则的`source`属性就能够拿到正则模式，通过正则的`flags`属性就能够拿到正则的参数。
```js
const a = /hi\d/ig;
console.log(a.source); //   hi\d
console.log(a.flags);  //   ig
```
因此，我们深拷贝一个正则实际上就是拿到这两部分，然后重新创建一个新的正则，从而实现跟原来的正则相同的功能即可。
```js
function deepClone(target) {
    if (target instanceof Object) {
        let dist;
        if (target instanceof Array) {
            //拷贝数组
            dist = [];
        } else if (target instanceof Function) {
            //拷贝函数
            dist = function() {
                return target.call(this, ...arguments);
            }
        } else if (target instanceof RegExp) {
            //拷贝正则
            dist = new RegExp(target.source, target.flags);
        } else {
            //拷贝对象
            dist = {};
        }
        for(let key in target) {
            dist[key] = deepClone(target[key]);
        }
        return dist;
    } else {
        return target;
    }
}
```
### 2.2.6拷贝复杂对象--日期
如果拷贝的是一个日期，在通过我们上面的方法拷贝后，返回的是一个字符串。这个字符串不是`Date`类型的，
它无法调用`Date`的任何方法。因此，我们需要支持日期格式的拷贝。事实上，通过上面的`Array,Function,RexExp`复杂对象类型的拷贝，我们可以发现，实际上这些拷贝都是通过`new XXX()`，相当于创建一个新的对象返回回去。因此，日期的拷贝也是一样：
```js
dist = new Date(source);
```
将要拷贝的日期，作为参数然后生成一个新的Date。最终实现如下：
```js
function deepClone(target) {
    if (target instanceof Object) {
        let dist;
        if (target instanceof Array) {
            //拷贝数组
            dist = [];
        } else if (target instanceof Function) {
            //拷贝函数
            dist = function () {
                return target.call(this, ...arguments);
            }
        } else if (target instanceof RegExp) {
            //拷贝正则
            dist = new RegExp(target.source,target.flags);
        } else if (target instanceof Date) {
            //拷贝日期
            dist = new Date(target);
        } else {
            // 拷贝对象
            dist = {};
        }
        for (let key in target) {
            dist[key] = deepClone(target[key]);
        }
        return dist;
    } else {
        return target;
    }
}
```
好了，到目前为止我们的深拷贝已经支持了简答数据类型，普通对象，数组，函数，正则，日期这些最常见的数据了。虽然我们的代码中有很多`if else`结构，但是我觉得这是最容易让大家理解的写法。


作者：海因斯坦
链接：https://juejin.im/post/6889327058158092302
来源：掘金