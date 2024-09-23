---
title: 基本认识
date: 2020-11-23 15:49:19
tag: nodejs
toc: true
---

## 模块导出
```js
const name = "张三";
const age = 20;
function say() {
    console.log(`你好，我叫${name},今年${age}岁了`);
}
//模块导出
exports.name = name;
exports.age = age;
exports.say = say;
```

## 类
```js
// 定义类
class Person {
    //定义类的字段
    name = '张三'
    //定义一个方法
    say() {
        console.log(`大家好,我叫${this.name}`);
    }
}

//实例化类
let person = new Person();
console.log(person.name);
person.say();
```

## 静态方法
* 类相当于实例的原型，所有在类中定义的方法，都会被实例继承。
* 如果在一个方法前，加上`static`关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。
* 注意，如果静态方法包含`this`关键字，这个`this`指的是类，而不是实例
* 静态方法和普通方法的区别
* 1、如果静态方法包含`this`关键字，这个this指的是类，而不是实例。注意与类中普通方法：类的方法内部如果含有this，它默认指向类的实例。
* 2、静态方法不能被实例调用，只能通过类来调用。
```js
class Person {
    //静态字段
    static name = '张三'
    //定义一个静态方法
    static say() {
        // console.log(`大家好,我叫${this.name}`);不使用this.name--普通方法this指向实例,静态方法this指向类
        console.log(`大家好,我叫${Person.name}`);
    }
}
```

## 构造方法
>每一个类都会有一个构造方法`constructor`，如果没有手动给类添加构造方法，那么系统会自动提供一个无参的构造方法
在js中，一个类的构造方法只能有一个;js中是没有重载方法
```js
class Person {
    constructor(name, sex) {
        this.name = name;
        this.sex = sex;
        // console.log('实例化对象');
    }
    say() {
        console.log(`我叫${this.name},我是${this.sex}`)
    }
}
//new Person();//实例化对象--new Person()相当于调用构造方法
new Person('张三','man').say();//我叫张三,我是man
```

## 类的继承
```js
class Person {
    constructor(name, sex) {
        this.name = name;
        this.sex = sex;
    }
    say() {
        console.log(`我叫${this.name},我是${this.sex}`)
    }
}
//学生类--继承Person
//extends ===> 扩展
class Student extends Person{
    constructor(name, sex, age) {
        //***注意：子类如果手动创建了构造方法的话，必须在第一行调用父类的构造方法
        super(name, sex);//子类必须调用父类的构造方法--父类已经定义过了，可以添加参数直接使用
        this.age = age;
    }
    //会覆盖父类的方法
    say() {
        //super-->父类对象
        super.say();
        console.log(`今年${this.age}岁`)
    }
}
new Student('李四', '男孩', '20').say();//我叫李四,我是男孩,今年20岁
```

## 类模块导出
**如果只有一个类可以直接使用`module.exports`**
```js
module.exports = class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    say() {
        console.log(`姓名：${this.name}--年龄：${this.age}`)
    }
}
```
**如果想导出两个类的话使用exports.xxx = xxx**
```js
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    say() {
        console.log(`姓名：${this.name}--年龄：${this.age}`)
    }
}
class Student {
    say() {
        console.log(`Student 类`)
    }
}
exports.Person = Person;
exports.Student = Student;
```