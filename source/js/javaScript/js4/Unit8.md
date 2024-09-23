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

### 读取属性的特性
>Object.getOwnPropertyDescriptor()方法。可以取得指定属性的描述符
>Object.getOwnPropertyDescriptors()方法。这个方法会在每个属性上调用Object.getOwnPropertyDescriptor()并在一个新对象中返回它们。
```js
let obj = {};
Object.defineProperties(obj, {
    year_: {
        value: 2020
    },
    eidtion: {
        value: 1
    },
    year: {
        get: function () {
            return this.year_;
        },
        set: function(newValue) {
            if (newValue > 2017) {
                this.year_ = newValue;
                this.edition += newValue-2017;
            }
        }
    }
});
let descriptor = Object.getOwnPropertyDescriptor(obj, 'year_');
console.log(descriptor.value);//2020
console.log(descriptor.configurable);//false
console.log(descriptor.writable);//false
console.log(descriptor.enumerable);//false
console.log(typeof descriptor.get)// ? 'undefined'
let descriptor2 = Object.getOwnPropertyDescriptor(obj, 'year');
console.log(descriptor2.value);// ? 'undefined'
console.log(descriptor2.enumerable);//false
console.log(typeof descriptor2.get);//function
// todo
console.log(Object.getOwnPropertyDescriptors(obj));
/*
{
  year_: {
    value: 2020,
    writable: false,
    enumerable: false,
    configurable: false
  },
  eidtion: { value: 1, writable: false, enumerable: false, configurable: false },
  year: {
    get: [Function: get],
    set: [Function: set],
    enumerable: false,
    configurable: false
  }
}
*/
```

### 合并(merge)对象
>`Object.assign()`方法。这个方法接收一个目标对象和一个或多个源对象作为参数，
>然后将每个源对象中可枚举`(Object.propertyIsEnumerable()返回true)`和自有`(Object.hasOwnProperty()返回true)`属性复制到目标对象.
>对每个符合条件的属性，这个方法会使用源对象上的[[Get]]取得属性的值,然后使用目标对象上的[[Set]]设置属性的值。
```js
let desc,src,result;
/**
 * 简单复制
 */
desc = {};
src = { id: 'src' };
result = Object.assign(desc, src);
/**
 * Object.assign修改目标对象
 * 也会返回修改后的目标对象
 */
console.log(result === desc);//true
console.log(desc !== src);//true
console.log(result);//{ id: 'src' }
console.log(desc);//{ id: 'src' }
/**
 * 多个源对象
 */
let result2,desc2;
desc2 = {};
result2 = Object.assign(desc2, {a: 'a'}, {b: 'b'});
console.log(result2);//{ a: 'a', b: 'b' }
```

### 对象标识及想等判定
>Object.is()方法
```js
//这些事===符合预期的情况
console.log(true === 1);//false
console.log(Object.is(true, 1))//false
console.log({} === {});//false
console.log(Object.is({}, {}))//false
console.log('2'===2);//false
console.log(Object.is('2', 2))//false
// !
console.log(+0 === -0);//true
console.log(Object.is(+0,-0));//false
console.log(0 === +0);//true
console.log(0 === -0);//true
//确定NaN相等性使用isNaN()
console.log(NaN === NaN);//false
console.log(Object.is(NaN, NaN))//true
console.log(isNaN(NaN));//true
```

### 增强的对象语法
```js
// 1、属性值简写
let name = 'marry';
let person2 = {
    name: name
}
console.log(person2)//{name: 'marry}
// todo
let person3 = {
    name
}
console.log(person3)//{name: 'marry}
//2、可计算属性-->在引入可计算属性前，如果想使用变量的值作为属性，那么必须先声明对象，然后使用中括号语法来添加属性。
const nameKey = 'name';
const agekey = 'age';
let person4 = {};
person4[nameKey] = 'Marry';
person4[agekey] = 20;
console.log(person4);//{ name: 'Marry', age: 20 }
//可计算属性--可以在对象字面量中完成动态属性赋值，中括号包围的对象属性键告诉运行时将其作为javaScript表达式而不是字符串来求值
let person5 = {
    [nameKey]: 'Marry',
    [agekey]: 18
}
console.log(person5);//{ name: 'Marry', age: 18 }
//因为被当做javascript表达式求值，所以可计算属性本身可以是复杂的表达式,在实例化时在求值
let uniqueToken = 0;
function getUniqueKey(key) {
    return `${key}_${uniqueToken++}`;
}
let person6 = {
    [getUniqueKey(nameKey)]: 'Marry',
    [getUniqueKey(agekey)]: 19
}
console.log(person6);//{ name_0: 'Marry', age_1: 19 }
//3、简写方法名
//在给对象定义方法时，通常要写一个方法名、冒号、然后再引用一个匿名函数表达式
let person7 = {
    sayName: function(name) {
        console.log(`my name is ${name}`);
    }
}
person7.sayName('marry');//my name is marry
// todo 简写
let person8 = {
    sayName(name) {
        console.log(`my name is ${name}`)
    }
}
person8.sayName('Marry');//my name is Marry
// todo 简写方法名和计算属性键相互兼容
const methodKey = 'sayName';
let person9 = {
    [methodKey](name) {
        console.log(`my name is ${name}`)
    }
}
person9.sayName('Marrymarry');//my name is Marrymarry
```