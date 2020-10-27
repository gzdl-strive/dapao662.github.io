---
title: 自执行匿名函数
date: 2020-10-27 15:58:12
tag: js
---

>自执行函数，即定义和调用合为一体。我们创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象

**下面2个括弧()都会立即执行**
```js
(function () {console.log('method--A')} ()); // 推荐使用这个 
(function () {console.log('method--B')})();// 但是这个也是可以用的
```

由于**括弧()和JS的&&，异或，逗号等操作符**是在函数表达式和函数声明上消除歧义的  所以一旦解析器知道其中一个已经是表达式了，其它的也都默认为表达式了.
```js
var i = function () {console.log('()')}();
true && function () {console.log('&&')}();
0,function () {console.log(',')}();
```

**如果你不在意返回值，或者不怕难以阅读你甚至可以在function前面加一元操作符号**
```js
!function () {console.log('!')}();
~function () {console.log('~')}();
-function () {console.log('-')}();
+function () {console.log('+')}();
```

**还有一个情况，使用new关键字,也可以用，但我不确定它的效率**
```js
new function () {console.log('new关键字')};
new function () {console.log('new传递参数')}(10);// 如果需要传递参数，只需要加上括弧()
```