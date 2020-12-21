---
title: 第八章-对象、类与面向对象编程
date: 2020-12-19 20:05:10
tag: js
---

## 类

### 1、定义类-和定义函数类似-两种方式：类声明和类表达式
**类声明**
```js
class P1 {};
```
**类表达式**
```js
const P2 = class {};
```
**与函数表达式类似，类表达式在他们被求值前也不能引用。不过，与函数定义不同的是，虽然函数声明可以提升，但类定义不能.**
```js
// 函数
console.log(FunctionExpression);//undefined
var FunctionExpression = function() {};
console.log(FunctionExpression);//[Function: FunctionExpression]

console.log(FunctionDeclaration);//[Function: FunctionDeclaration]
function FunctionDeclaration() {};
console.log(FunctionDeclaration);//[Function: FunctionDeclaration]

//类
console.log(ClassExpression);//undefined
var ClassExpression = class {};
console.log(ClassExpression);//[Function: ClassExpression]

console.log(ClassDeclaration);//Cannot access 'ClassDeclaration' before initialization
class ClassDeclaration {}
console.log(ClassDeclaration);//[Function: ClassDeclaration]
```

**另一个和函数声明不同的地方是，函数受函数作用域限制，而类受块作用域限制**
```js
{
    function FunctionDeclaration2() {}
    class ClassDeclaration2 {}
}
console.log(FunctionDeclaration2);//[Function: FunctionDeclaration2]
console.log(ClassDeclaration2);//ClassDecalaration2 is not defined
```

### 2、类可以包含构造函数方法、实例方法、获取函数、设置函数和静态类方法

#### (1)类构造函数
>`constructor`关键字用于在类定义块内部创建类的构造函数。方法名constructor会告诉解释器在使用new操作符创建类的新实例时，应该调用这个函数.构造函数的定义不是必须的，不定义构造函数相当于将构造函数定义为空函数
**实例化-使用`new`操作符实例化`Person`的操作等于使用`new`调用其构造函数。**
**类是一种特殊函数**

#### (2)实例、原型和类成员
```js
class Person {
    //构造函数
    constructor() {
        //添加到this的所有内容都会存在于不同的实例上
        this.locate = () => console.log('instance',this)
    }
    //原型方法
    locate() {
        console.log('prototype', this);
    }
    //静态方法
    static locate() {
        console.log('class', this);
    }
}
let p = new Person();
p.locate();//instance Person { locate: [Function] }
Person.prototype.locate();//prototype Person {}-原型方法
p.__proto__.locate();//prototype Person {}
Person.locate();//class [Function: Person]-静态方法-存在类上面
```

#### (3)类定义语法支持在原型和类上定义生成器方法
```js
class Person1 {
    //在原型上定义生成器方法
    * createGenerator() {
        yield '百兽凯多';
        yield 'Big Mom';
        yield '黑胡子';
        yield '红发香克斯';
    }
    //在类上定义生成器方法
    static * createGenerator() {
        yield '鱼鱼果实幻兽种',
        yield '魂魂果实',
        yield '暗暗果实&震震果实',
        yield '面子果实'
    }
}
//静态方法
let jobIter = Person1.createGenerator();
console.log(jobIter.next().value);//鱼鱼果实幻兽种
console.log(jobIter.next().value);//魂魂果实
console.log(jobIter.next().value);//暗暗果实&震震果实
console.log(jobIter.next().value);//面子果实
//原型方法
let p2 = new Person1();
let nickname = p2.createGenerator();
console.log(nickname.next().value);//百兽凯多
console.log(nickname.next().value);//Big Mom
console.log(nickname.next().value);//黑胡子
console.log(nickname.next().value);//红发香克斯
```
**因为支持生成器方法，所以可以添加一个默认的迭代器，把类实例变成可迭代对象:**
```js
class Person2 {
    constructor() {
        this.nickname = ['路飞', '索隆', '娜美', '山治', '乌索普'];
    }
    *[Symbol.iterator] () {
        yield *this.nickname.entries();
    }
}
let p3 = new Person2();
for (let [key, nickname] of p3) {
    console.log(nickname);
}
// 路飞
// 索隆
// 娜美
// 山治
// 乌索普
```

#### 3、继承
>使用`super`注意事项

(1)`super`只能在派生类构造函数和静态方法中使用
```js
class Vehicle {
    constructor() {
        super();//'super' keyword unexpected here
    }
}
```
(2)不能单独引用`super`关键字，要么用它调用构造函数，要么用它引用静态方法
```js
class Vehicle {}
class Bus extends Vehicle {
    constructor() {
        console.log(super);
    }
}
```
(3)调用`super()`会调用父类构造函数，并将返回的实例赋值给this
```js
class Vehicle {}
class Bus extends Vehicle {
    constructor() {
        super();
        console.log(this instanceof Vehicle);
    }
}
new Bus();//true
```
(4)`super`的行为如同调用构造函数，如果需要给父类构造函数传参，则需要手动传入
```js
class Vehicle2 {
    constructor(params) {
        this.license = params;
    }
}
class Bus2 extends Vehicle2 {
    constructor(params) {
        super(params);
    }
}
console.log(new Bus2('海贼王'));//Bus2 { license: '海贼王' }
```
(5)如果没有定义类构造函数，在实例化派生类时会调用`super()`,而且会传入所有传给派生类的参数
(6)在类构造函数中，不能在调用`super()`之前引用`this`
```js
class Vehicle3 {};
class Bus3 extends Vehicle3 {
    constructor() {
        console.log(this);
    }
}
new Bus3();//Must call super constructor in derived class before accessing 'this' or returning from derived constructor
```
(7)如果在派生类中显示定义了构造函数，则要么必须在其中调用`super`，要么必须在其中返回一个对象。