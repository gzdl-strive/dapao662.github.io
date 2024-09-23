---
title: 条件判断下的变量提升
date: 2021-03-10 15:48:24
tag: js
---

### if else条件判断下的变量提升
```js
console.log(a);//undefined
if (false) {
    var a = 'if else';
}
console.log(a);//undefined
```
**在当前作用域中不管条件是否成立都会进行变量提升**
![4](/assets/jsImg/variablePromotion/4.png "4")

### if else条件判断下函数变量提升的坑
#### 新版浏览器中，在条件判断块级作用域之外使用条件内函数
```js
console.log(print3());
if (true) {
    function print3() {
        console.log('print3');
    }
}
console.log(print3());
/*
输出:
undefined
print3
undefined
*/
```

#### 新版浏览器中，在条件判断块级作用域中使用条件内函数
```js
if (true) {
    console.log(print4());
    function print4() {
        console.log('print4');
    }
}
console.log(print4());
/*
输出：
print4
undefined
print4
*/
```
>思路：在 if else 中带 function 的函数同样也会先被声明和定义所以条件判断中的 print() 可以直接使用

![5](/assets/jsImg/variablePromotion/5.png "5")

### 思考题
**if语句中的变量提升**
```js
if (!('value' in window)) {
    var value = 2021;
}
console.log(value);
console.log('value' in window);
/* 输出
    undefined
    true
*/
```
>思路：和上面所说的一样，不管条件是否成立带 var 的变量提升，当前在全局作用域 value 就是 window 的属性，所以结果显而易见输出 undefined 和 true

```js
if(("value" in window)){
    var value = 2021; 
}
console.log(value); 
console.log('value' in window); 
/* 输出
    2021
    true
*/
```

