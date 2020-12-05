---
title: 第八章-对象、类与面向对象编程
date: 2020-12-05 13:21:10
tag: js
---

## 对象

### 属性的类型

> ECMA-262使用一些内部特征来描述属性的特征--比如 `[[Enumerable]]` 属性分成两种：数据属性和访问器属性

#### 1、数据属性-数据属性包含一个保存数据值的位置--数据属性有4个特性描述它们的行为

(1) `[[Configurable]]` 表示属性是否可以通过delete删除并重新定义, 是否可以修改它的特性，以及是否可以把它改成访问器属性. 默认true
(2) `[[Enumerable]]` 表示属性是否可以通过for-in循环返回。默认true
(3) `[[Writable]]` 表示属性的值是否可以被修改. 默认true
(4) `[[Value]]` 包含属性实际的值。默认undefined
**在显示的添加属性到对象后， `[[Configurable]]、[[Enumerable]]、[[Writable]]` 默认true, 而 `[[Value]]` 被设置为指定的值.**
**要修改属性的默认特性- `Object.defineProperty()` 方法。这个方法接收3个参数: 要给其添加属性的对象，属性名称，描述符对象. 最后一个参数（描述符对象）上的属性可以包含: `configurable、enumerable、writable、value` **

``` js
let person = {};
Object.defineProperty(person, "name", {
    writable: false,
    value: 'Hello'
})
console.log(person.name); //Hello
person.name = "World";
console.log(person.name); //Hello
```

**注意：在调用 `Object.defineProperty()` 时， `configurable、enumerable、writable` 如果不指定，都默认为false**

#### 2、访问器属性-访问器属性不包含数据值. 相反它们包含一个获取(getter)函数和一个设置函数(setter)，不过这两个函数不是必须的.

访问器属性有4个特性描述它们的行为:
(1) `[[Configurable]]` 表示属性是否可以通过 `delete` 删除并重新定义, 是否可以修改它的特性，以及是否可以把它改成数据属性. 默认true
(2) `[[Enumerable]]` 表示属性是否可以通过 `for-in` 循环返回。默认 `true`
(3) `[[Get]]` 获取函数，在读取属性时调用。默认值为 `undefined.`
(4) `[[Set]]` 设置函数, 在写入属性时调用。默认值为 `undefined.`

``` js
let book = {
    year_: 2017,
    edition: 1
}
Object.defineProperty(book, 'year', {
    get() {
        return this.year_;
    },
    set(newValue) {
        if (newValue > 2017) {
            this.year_ = newValue;
            this.edition += newValue - 2017;
        }
    }
});
book.year = 2018;
console.log('book-edition:', book.edition); //2
```

### 定义多个属性

> `Object.defineProperties()` 方法。这个方法可以通过多个描述符一次性定义多个属性. 接收两个参数：要为之添加或修改属性的对象和另一个描述符对象, 其属性与要添加或修改的属性一一对应

``` js
let people = {};
Object.defineProperties(people, {
    year_: {
        value: 2017
    },
    edition: {
        value: 1
    },
    year: {
        get() {
            return this.year_;
        },
        set(newValue) {
            if (newValue > 2017) {
                this.year_ = newValue;
                this.edition += newValue - 2017;
            }
        }
    }
});
people.year = 2019;
console.log(people.edition); //1--与上面的区别是所有属性都是同时定义的，并且数据属性的configurable、enumerable、和value都是false
```
