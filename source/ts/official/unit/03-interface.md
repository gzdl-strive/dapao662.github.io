---
title: ts-接口
date: 2021-01-25 20:40:02
tag: ts
toc: true
---

> TypeScript的核心原则之一是对值所具有的结构进行类型检查, 它有时被称做“鸭式辨型法”或“结构性子类型化". 在TypeScript里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约

### 接口初探

``` ts
function printLable(labelledObj: {label: string}) {
    console.log(labelledObj.label);
}
let myObj = { size: 10, label: 'size 10 object' };
printLable(myObj);//size 10 object
```

**类型检查器会查看 `printLabel` 的调用。 `printLabel` 有一个参数，并要求这个对象参数有一个名为label类型为string的属性。需要注意的是，我们传入的对象参数实际上会包含很多属性，但是编译器只会检查那些必需的属性是否存在，并且其类型是否匹配。 然而，有些时候TypeScript却并不会这么宽松，我们下面会稍做讲解。**

**重写上述例子->使用接口来描述**

``` ts
interface labelledValue {
    label: string;
}
function printLabel(labelledObj: labelledValue) {
    console.log(labelledObj.label);
}
let myObj = { size: 20, 'size 20 Object '};
printLabel(myObj);//size 20 Object
```

** `labelledValue` 接口就好比一个名字，用来描述上面例子里的要求. 它代表了有一个 label属性且类型为string的对象。需要注意的是，我们在这里并不能像在其它语言里一样，说传给 printLabel的对象实现了这个接口. 我们只会去关注值的外形. 只要传入的对象满足上面提到的必要条件，那么它就是被允许的**

> 还有一点值得提的是，类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以

### 可选属性

> 接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了

**面是应用了“option bags”的例子:**

``` ts
interface SquareConfig {
    color?: string;
    width?: number;
}
function createSquare(config: SquareConfig): { color: string, area: number} {
    let newSquare = { color: 'white', area: 100 };
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
let mySquare = createSquare({ color: 'black '});
console.log(mySquare);//{ color: 'black ', area: 100 }

```
>带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个`?符号`.可选属性的`好处之一是可以对可能存在的属性进行预定义`，`好处之二是可以捕获引用了不存在的属性时的错误`
**比如，我们故意将 createSquare里的color属性名拼错，就会得到一个错误提示**

### 只读属性
>一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用` readonly`来指定`只读属性`
```ts
interface Point {
    readonly x: number;
    readonly y: number;
}
```
**你可以通过赋值一个对象字面量来构造一个Point。 赋值后， x和y再也不能被改变了**
```ts
let p1: Point = { x: 10, y: 20 };
console.log(p1);
// p1.x = 30;error:无法分配到 "x" ，因为它是只读属性
```
**`TypeScript`具有`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改**
```ts
let a: number[] = [1, 2, 6, 9];
let ro: ReadonlyArray<number> = a;
a[0] = 5;
// ro[0] = 5;error ! 类型“readonly number[]”中的索引签名仅允许读取
a.push(10);
// ro.push(5); error! 类型“readonly number[]”上不存在属性“push”
a.length = 50;
// ro.length = 50;// error !无法分配到 "length" ，因为它是只读属性。
// a = ro; error ! 类型 "readonly number[]" 为 "readonly"，不能分配给可变类型 "number[]"
```
**上面代码的最后一行，可以看到就算把整个`ReadonlyArray`赋值到一个普通数组也是不可以的。**
**但是你可以用类型断言重写：**
```ts
a = ro as number[];
a = <number[]>ro;
```
**readonly vs const**
>最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。`做为变量`使用的话用` const`，若`做为属性`则使用`readonly`。


### 额外的属性检查
```ts
interface SquareConfig2 {
    color?: string;
    width?: number;
}
function createSquare2(config: SquareConfig2) : { color: string, area: number } {
    let newSquare = { color: 'White', area: 100 };
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
// let mySquare2 = createSquare2({ coluor: 'red', width: 20 });
```
**类型“`{ coluor: string; width: number; }`”的参数不能赋给类型“SquareConfig2”的参数。对象文字只能指定已知的属性，但“coluor”中不存在类型“SquareConfig2”。是否要写入**
*TypeScript会认为这段代码可能存在bug*
**对象字面量会被特殊对待而且会经过 额外属性检查，当将它们赋值给变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误**
* 绕开这些检查非常简单。 最简便的方法是使用类型断言：
```ts
let mySquare2 = createSquare2({ width: 100, opacity: 0.5 } as SquareConfig2);
```
>然而，最佳的方式是能够添加一个字符串索引签名，前提是你能够确定这个对象可能具有某些做为特殊用途使用的额外属性
**如果 `SquareConfig2`带有上面定义的类型的`color`和`width`属性,并且还会带有任意数量的其它属性，那么我们可以这样定义它：**

```ts
interface SquareConfig3 {
    color: string;
    width: number;
    [propName: string]: any;
}
```
**我们稍后会讲到索引签名，但在这我们要表示的是SquareConfig可以有任意数量的属性，并且只要它们不是color和width，那么就无所谓它们的类型是什么。**
* 还有最后一种跳过这些检查的方式，这可能会让你感到惊讶，它就是将这个对象赋值给一个另一个变量
**因为 `squareOptions`不会经过额外属性检查，所以编译器不会报错**
```ts
let squareOptions = { coluor: 'red', width: 100 };
let mySquare2 = createSquare2(squareOptions);
```
**要留意，在像上面一样的简单代码里，你可能不应该去绕开这些检查,对于包含方法和内部状态的复杂对象字面量来讲，你可能需要使用这些技巧，但是大部额外属性检查错误是真正的bug。就是说你遇到了额外类型检查出的错误，比如“option bags”，你应该去审查一下你的类型声明。在这里，如果支持传入 color或colour属性到createSquare.你应该修改SquareConfig定义来体现出这一点**


### 函数类型
>接口能够描述`JavaScript中对象`拥有的各种各样的外形。 除了描述带有属性的普通对象外，接口也可以`描述函数类型`。为了使用接口表示函数类型，我们需要给接口定义一个调用签名。它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。
```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
//对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配
mySearch = function(src: string, sub: string): boolean {
    //search()方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串.如果没有找到任何匹配的子串，则返回 -1
    let result = src.search(sub);
    return result > -1;
}
```
**函数的参数会逐个进行检查，要求对应位置上的参数类型是兼容的。如果你不想指定类型，TypeScript的类型系统会推断出参数类型，因为函数直接赋值给了 SearchFunc类型变量。函数的返回值类型是通过其返回值推断出来的（此例是 false和true）.如果让这个函数返回数字或字符串，类型检查器会警告我们函数的返回值类型与 SearchFunc接口中的定义不匹配。**
```ts
let mySearch2: SearchFunc;
//TypeScript的类型系统会推断出参数类型
//因为函数直接赋值给了 SearchFunc类型变量
mySearch2 = function(src, sub) {
    let result = src.search(sub);
    //函数的返回值类型是通过其返回值推断出来的
    return result > -1;
}
```

### 可索引的类型
>与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如a[10]或ageMap["daniel"],可索引类型具有一个 索引签名，它描述了对象索引的类型，还有相应的索引返回值类型。 
```ts
interface StringArray {
    [index: number]: string;
    //[propsName: string]: any;
}
let myArray: StringArray;
myArray = ['Bob', 'Fred'];
let myStr: string = myArray[0];
```
**上面例子里，我们定义了`StringArray`接口，它具有索引签名.这个索引签名表示了当用 `number`去`索引StringArray`时会得到`string类型的返回值`**
> TypeScript支持两种索引签名：**字符串和数字**
**可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型.这是因为当使用 number来索引时，JavaScript会将它转换成string然后再去索引对象.也就是说用 100（一个number）去索引等同于使用"100"（一个string）去索引，因此两者需要保持一致**
```ts
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}
interface NotOkay {
    // [x: number]: Animal;数字索引类型“Animal”不能赋给字符串索引类型“Dog”
    [x: number]: Dog;//ok
    [y: string]: Dog;
}
```
**字符串索引签名能够很好的描述`dictionary模式`,并且它们也会确保所有属性与其返回值类型相匹配.因为字符串索引声明了 `obj.property`和`obj["property"]`两种形式都可以**
```ts
interface NumberDictionary {
    [index: string]: number;
    length: number;
    // name: string;类型“string”的属性“name”不能赋给字符串索引类型“number”
}
```
**最后，你可以将索引签名设置为只读，这样就防止了给索引赋值**
```ts
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray2: ReadonlyStringArray = ['str', 'rgba'];
// myArray2[2] = 'rush';类型“ReadonlyStringArray”中的索引签名仅允许读取
```

### 类类型
> 实现接口:与`C#`或`Java`里接口的基本作用一样，`TypeScript`也能够用它来明确的强制一个类去符合某种契约。
```ts
interface ClockInterface {
    currentTime: Date;
}
class Clock implements ClockInterface {
    currentTime: Date;
    constructor(h: number, m: number){

    }
}
```
**你也可以在接口中描述一个方法，在类里实现它，如同下面的`setTime`方法一样：**
```ts
interface ClockInterface2 {
    currentTime: Date;
    setTime(d: Date): void;
}
class Clock2 implements ClockInterface2 {
    currentTime: Date;
    setTime(d: Date): void {
        this.currentTime = d;
    }
    constructor(h: number, m: number) {}
}
```
**接口描述了类的公共部分，而不是公共和私有两部分。 它不会帮你检查类是否具有某些私有成员**

#### 类静态部分与实例部分的区别
>当你操作类和接口的时候，你要知道类是具有两个类型的：静态部分的类型和实例的类型.你会注意到，当你用构造器签名去定义一个接口并试图定义一个类去实现这个接口时会得到一个错误
```ts
interface ClockConstructor {
    new (hour: number, minute: number);
}
// 类“Clock3”错误实现接口“ClockConstructor”。类型“Clock3”提供的内容与签名“new (hour: number, minute: number): any”不匹配
// class Clock3 implements ClockConstructor {
//     private currentTime: Date;
//     constructor(h: number, m: number){}
// }
```
**这里因为当一个类实现了一个接口时，只对其实例部分进行类型检查. constructor存在于类的静态部分，所以不在检查的范围内.因此，我们应该直接操作类的静态部分**
```ts
interface ClockConstructor2 {
    new (hour: number, minute: number): ClockInterface3;
}
interface ClockInterface3 {
    tick();
}
function createClock(ctor: ClockConstructor2, hour: number, minute: number): ClockInterface3 {
    return new ctor(hour, minute);
}
class DigitalClock implements ClockInterface3 {
    constructor(h: number, m: number) {}
    tick() {
        console.log('beep beep');
    }
}
class AnalogClock implements ClockInterface3 {
    constructor(h: number, m: number){}
    tick() {
        console.log('tick tick');
    }
}
// 因为createClock的第一个参数是ClockConstructor类型，在createClock(AnalogClock, 7, 32)里，会检查AnalogClock是否符合构造函数签名
let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```


### 继承接口
>和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。
```ts
interface Shape {
    color: string;
}
interface Square extends Shape {
    sideLength: number;
}
let square = <Square>{};
square.color = 'blue';
square.sideLength = 20;
// ?一个接口可以继承多个接口，创建出多个接口的合成接口
interface Shape2 {
    color: string;
}
interface PenStroke {
    penWidth: number;
}
interface Square2 extends Shape2, PenStroke {
    sideLength: number;
}
let square2 = <Square2>{};
square2.color = 'red';
square2.penWidth = 10;
square2.sideLength = 5.0;
```

### 混合类型
>一个例子就是，一个对象可以同时做为函数和对象使用，并带有额外的属性
```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function getCounter(): Counter {
    let counter = <Counter>function (start: number) {};
    counter.interval = 123;
    counter.reset = function() {
        console.log('abc');
    }
    return counter;
}
let str = getCounter();
str(10);
str.reset();
str.interval = 5.0;
```


### 接口继承类
>当接口继承了一个类类型时，它会继承类的成员但不包括其实现.就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。`接口`同样会`继承到类`的`private`和`protected`成员。这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，`这个接口类型只能被这个类或其子类所实现（implement）`.当你有一个庞大的继承结构时这很有用，但要指出的是你的代码只在子类拥有特定属性时起作用。 这个子类除了继承至基类外与基类没有任何关系。
```ts
class Control {
    private state: any;
}
interface SelectableControl extends Control {
    select(): void;
}
class Button extends Control implements SelectableControl {
    select() {}
}
class TextBox extends Control {
    select();
}
//类“Image2”错误实现接口“SelectableControl”。
//类型 "Image2" 中缺少属性 "state"，但类型 "SelectableControl" 中需要该属性
// class Image2 implements SelectableControl {
//     select() {}
// }
```
在上面的例子里，`SelectableControl`包含了`Control`的所有成员，`包括私有成员state`。因为 `state`是私有成员，所以只能够是`Control的子类们`才能实现`SelectableControl接口`。 `Image2`不是`Control`的子类.因为只有 `Control`的子类才能够拥有一个声明于`Control的私有成员state`，这对私有成员的兼容性是必需的。在`Control`类内部，是允许通过`SelectableControl`的实例来`访问私有成员state`的.实际上，` SelectableControl`接口和拥有`select方法的Control类`是一样的。`Button`和`TextBox`类是`SelectableControl`的子类`（因为它们都继承自Control并有select方法）`，但`Image`和`Location`类并不是这样的