---
title: ts-变量声明
date: 2021-01-22 16:02:02
tag: ts
toc: true
---

## var声明
```ts
function f() {
    var a = 1; 
    a = 2;
    var b = g();
    a = 3;
    return b;
    function g() {
        return a;
    }
}
f();//2
```
### var作用域规则
```ts
function f2(init: boolean) {
    if (init) {
        var x = 10;
    }
    return x;
}
console.log(f2(true));//10
console.log(f2(false));//undefined
```
**变量 x是定义在*if语句里面*，但是我们却可以在语句的外面访问它.这是因为 var声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问.包含它的代码块对此没有什么影响。 有些人称此为* var作用域或函数作用域*。 函数参数也使用函数作用域**
*这些作用域规则可能会引发一些错误。 其中之一就是，多次声明同一个变量并不会报错*

### var捕获变量怪异之处
```ts
for(var i = 0; i < 5; i++) {
    setTimeout(() => console.log(i), 100 * i);//5 5 5 5 5 
}
```
**我们传给`setTimeout`的每一个函数表达式实际上都引用了相同作用域里的同一个i**
* 让我们花点时间思考一下这是为什么

**`setTimeout`在若干毫秒后执行一个函数，并且是在for循环结束后。 for循环结束后，i的值为10。 所以当函数被调用的时候，它会打印出 10**

* 一个通常的解决方法是使用立即执行的函数表达式
```ts
for (var i = 0; i < 5; i++) {
    (function(i) {
        setTimeout(() => {
            console.log(i)
        }, i * 100);
    })(i);
}//0 1 2 3 4
```

**参数 i会覆盖for循环里的i，但是因为我们起了同样的名字，所以我们不用怎么改for循环体里的代码**

## 解构
```ts
let input: (number | string)[] = [1, 'hello'];
let [first, second] = input;
console.log(first, second);//1 hello
// 这创建了2个命名变量 first 和 second。 相当于使用了索引
first = input[0];
second = input[1];
console.log(first, second);//1 hello
// 解构作用于已声明的变量会更好：
[first, second] = [second, first];
console.log(first, second);//hello 1
// 作用于函数参数：
function f3([first, second]: [number, number]) {
    console.log(first, second);
}
f3([10, 20]);
// 你可以在数组里使用...语法创建剩余变量
let [first2, ...rest] = [1, 2, 3, 4];
console.log(first2);//1
console.log(rest);//[2, 3, 4]
```

### 对象解构
```ts
let o = {
    a: 'foo',
    b: 12,
    c: 'bar',
};
let { a, b } = o;
console.log(a, b);//foo 12
// 你可以在对象里使用...语法创建剩余变量
let o2 = {
    a2: 'hello',
    b2: 99,
    c2: 'abc'
}
let { a2, ...passthrough } = o2;
let total = passthrough.b2 + passthrough.c2;
console.log(total);//99abc
```

## 属性重命名
```ts
let { a: newName1, b: newName2 } = o;
console.log('属性重命名', newName1, newName2);//属性重命名 hello 99
```
**你可以将 `a: newName1` 读做 "`a 作为 newName1`"**
**方向是从左到右，好像你写成了以下样子：**
```ts
let newName1 = o.a;
let newName2 = o.b;
```
**令人困惑的是，这里的冒号不是指示类型的。 如果你想指定它的类型， 仍然需要在其后写上完整的模式。**
```ts
let o = {
    a: 'hello',
    b: 99,
    c: 'world'
}
let { a: newName1, b: newName2 } : { a: string, b: number } = o;
```

## 默认值
**默认值可以让你在属性为` undefined `时使用缺省值**
```ts
function keepWholeObject(wholeObject: { a: string, b?: number }): void {
    let { a, b = 1001 } = wholeObject;
    console.log(a, b);
}
const input4 = { a: '55' };
// 现在，即使 b 为 undefined ， keepWholeObject 函数的变量 wholeObject 的属性 a 和 b 都会有值。
keepWholeObject(input4);//'55', 1001
```

## 函数声明
**解构也能用于函数声明**
```ts
type C = { a: string, b?: number };
function f4({ a, b }: C): void {}
```
**但是，通常情况下更多的是指定默认值，解构默认值有些棘手。 首先，你需要在默认值之前设置其格式。**
```ts
function f5({ a = 'rush b', b = 0 } = {}): void {
    console.log(a, b);
}
f5();//rush b 0
```

## 展开
>展开操作符正与解构相反。 它允许你将一个数组展开为另一个数组，或将一个对象展开为另一个对象。

**展开对象后面的属性会覆盖前面的属性**
```ts
let defaults = { food: '牛', price: 666, animation: 'rotate'};
let search = { food: '羊', ...defaults };
let search2 = { ...defaults, food: '鱼' };
console.log(search);
console.log(search2);
```
* 对象展开还有其它一些意想不到的限制。 
* 首先，它仅包含对象 自身的可枚举属性。 
* 大体上是说当你展开一个对象实例时，你会丢失其方法
```ts
class C2 {
    p: number = 12;
    method() {
        console.log('我是method方法');
    }
}
let c = new C2();
c.method();//我是method方法
let clone = { ...c };
console.log(clone.p);//12
// clone.method();
```
**其次，TypeScript编译器不允许展开泛型函数上的类型参数**