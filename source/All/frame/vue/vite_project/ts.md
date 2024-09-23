---
title: TS
date: 2022-03-22 14:46:24
toc: true
---


## TS基础类型
>- 字符串使用string定义`let a: string = '123'`
>- 数字类型使用number定义
```ts
let notANumber: number = NaN;//NaN
let num: number = 123;//普通数字
let infinityNumber: number = Infinity;//无穷大
let decimal: number = 6;//十进制
let hex: number = 0xf00d;//十六进制
let binary: number = 0b1010;//二进制
let octal: number = 0o744;//八进制
```
>- 布尔类型boolean
**注意：使用构造函数Boolean创造的对象不是布尔值**
```ts
let createdBoolean: boolean = new Boolean(1)
//这样会报错 应为事实上 new Boolean() 返回的是一个 Boolean 对象 

let createdBoolean: Boolean = new Boolean(1);//正确

let booleand: boolean = true //可以直接使用布尔值
let booleand2: boolean = Boolean(1) //也可以通过函数返回布尔值
```
>- 空值void
void 类型的用法，主要是用在我们不希望调用者关心函数返回值的情况下，比如通常的异步回调函数

**void也可以定义undefined 和 null类型**
```ts
let u: void = undefined
let n: void = null;
```

>null和undefined类型
```ts
let u: undefined = undefined;//定义undefined
let n: null = null;//定义null
```
**void 和 undefined 和 null 最大的区别**
>与 void 的区别是，undefined 和 null 是所有类型的子类型。也就是说 undefined 类型的变量，可以赋值给 string 类型的变量
```ts
//这样写会报错 void类型不可以分给其他类型
let test: void = undefined
let num2: string = "1"
 
num2 = test

//这样是没问题的
let test: null = null
let num2: string = "1"
 
num2 = test
 
//或者这样的
let test: undefined = undefined
let num2: string = "1"
 
num2 = test
```

**如果你配置了tsconfig.json开启了严格模式**
```json
{
  "compilerOptions":{
    "strict": true
  }
}
```
那么，null不能赋予void类型, undefined可以赋予void类型

## 任意类型any
nodejs 环境执行ts
npm i @types/node --save-dev （node环境支持的依赖必装）
npm i ts-node -g

1. 没有强制限定哪种类型，随时切换类型都可以 我们可以对 any 进行任何操作，不需要检查类型
```ts
let anys:any = 123
anys = '123'
anys = true
```
2. 声明变量的时候没有指定任意类型默认为any
```ts
let anys;
anys = '123'
anys = true
```
3. 弊端如果使用any 就失去了TS类型检测的作用

4. TypeScript 3.0中引入的 unknown 类型也被认为是 top type ，但它更安全。与 any 一样，所有类型都可以分配给unknown
**unknow  unknow类型比any更加严格当你要使用any 的时候可以尝试使用unknow**
```ts
//unknown 可以定义任何类型的值
let value: unknown;
 
value = true;             // OK
value = 42;               // OK
value = "Hello World";    // OK
value = [];               // OK
value = {};               // OK
value = null;             // OK
value = undefined;        // OK
value = Symbol("type");   // OK
 
//这样写会报错,unknow类型不能作为子类型只能作为父类型 any可以作为父类型和子类型
//unknown类型不能赋值给其他类型
let names:unknown = '123'
let names2:string = names
 
//这样就没问题 any类型是可以的
let names:any = '123'
let names2:string = names   
 
//unknown可赋值对象只有unknown 和 any
let bbb:unknown = '123'
let aaa:any= '456'
 
aaa = bbb
```
```ts
// 如果是any类型在对象没有这个属性的时候还在获取是不会报错的
let obj:any = {b:1}
obj.a
 
 
// 如果是unknow 是不能调用属性和方法
let obj:unknown = {b:1,ccc:():number=>213}
obj.b
obj.ccc()
```

## 接口和对象类型
>在typescript中，我们定义对象的方式要用关键字`interface`（接口），我的理解是使用interface来定义一种约束，让**数据的结构满足约束的格式**。
```ts
//这样写是会报错的 因为我们在person定义了a，b但是对象里面缺少b属性
//使用接口约束的时候不能多一个属性也不能少一个属性
//必须与接口保持一致
interface Person {
    b:string,
    a:string
}
 
const person:Person  = {
    a:"213"
}
```
```ts
//重名interface  可以合并
interface A{name:string}
interface A{age:number}
var x:A={name:'xx',age:20}
//继承
interface A{
    name:string
}
 
interface B extends A{
    age:number
}
 
let obj:B = {
    age:18,
    name:"string"
}
```

### 可选属性使用?操作符
```ts
//可选属性的含义是该属性可以不存在
//所以说这样写也是没问题的
interface Person {
    b?:string,
    a:string
}
 
const person:Person  = {
    a:"213"
}
```

### 任意属性
>需要注意的是，一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
```ts
//在这个例子当中我们看到接口中并没有定义C但是并没有报错
//应为我们定义了[propName: string]: any;
//允许添加新的任意属性
interface Person {
    b?:string,
    a:string,
    [propName: string]: any;
}
const person:Person  = {
    a:"213",
    c:"123"
}
```

### 只读属性
readonly 只读属性是不允许被赋值的只能读取
```ts
//这样写是会报错的
//应为a是只读的不允许重新赋值
interface Person {
    b?: string,
    readonly a: string,
    [propName: string]: any;
}
 
const person: Person = {
    a: "213",
    c: "123"
}
 
person.a = 123
```

### 添加函数
```ts
interface Person {
    b?: string,
    readonly a: string,
    [propName: string]: any;
    cb():void
}
 
const person: Person = {
    a: "213",
    c: "123",
    cb:()=>{
        console.log(123)
    }
}
```

## 数组类型
### 1、类型[]
>类型加中括号
```ts
//类型加中括号
let arr:number[] = [123]
//这样会报错定义了数字类型出现字符串是不允许的
let arr:number[] = [1,2,3,'1']
//操作方法添加也是不允许的
let arr:number[] = [1,2,3,]
arr.unshift('1')
 
var arr: number[] = [1, 2, 3]; //数字类型的数组
var arr2: string[] = ["1", "2"]; //字符串类型的数组
var arr3: any[] = [1, "2", true]; //任意类型的数组
```

### 数组泛型
>规则: Array<类型>
```ts
let arr: Array<number> = [1, 2, 3];
```

### 用接口表示数组
>一般用来表示类数组
```ts
interface NumberArray {
  [index: number]: number;
}
let fibonacci: NumberArray = [1, 2, 3];
//表示：只要索引的类型是数字时，那么值的类型必须是数字
```

### 多维数组
```ts
let data:number[][] = [[1,2], [3,4]];
```

### arguments类数组
```ts
function Arr(...args:any): void {
    console.log(arguments)
    //错误的arguments 是类数组不能这样定义
    let arr:number[] = arguments
}
Arr(111, 222, 333)
 
function Arr(...args:any): void {
    console.log(arguments) 
    //ts内置对象IArguments 定义
    let arr:IArguments = arguments
}
Arr(111, 222, 333)
 
//其中 IArguments 是 TypeScript 中定义好了的类型，它实际上就是：
interface IArguments {
  [index: number]: any;
  length: number;
  callee: Function;
}
```

### arr在数组中的应用
>一个常见的例子数组中可以存在任意类型
```ts
let list: any[] = ['test', 1, [],{a:1}]
```

## 函数扩展
### 函数的类型
```ts
//注意，参数不能多传，也不能少传 必须按照约定的类型来
const fn = (name: string, age:number): string => {
    return name + age
}
fn('张三',18)
```
### 函数的可选参数?
```ts
//通过?表示该参数为可选参数
const fn = (name: string, age?:number): string => {
    return name + age
}
fn('张三')
```

### 函数参数的默认值
```ts
const fn = (name: string = "我是默认值"): string => {
    return name
}
fn()
```

### 接口定义函数
```ts
//定义参数 num 和 num2  ：后面定义返回值的类型
interface Add {
    (num:  number, num2: number): number
}
 
const fn: Add = (num: number, num2: number): number => {
    return num + num2
}
fn(5, 5)
 
interface User{
    name: string;
    age: number;
}
function getUserInfo(user: User): User {
  return user
}
```

### 定义剩余参数
```ts
const fn = (array:number[],...items:any[]):any[] => {
  console.log(array,items)
  return items
}
 
let a:number[] = [1,2,3]
 
fn(a,'4','5','6')
```

### 函数重载
>重载是方法名字相同，而参数不同，返回类型可以相同也可以不同
>如果参数类型不同，则参数类型应设置为 any
>参数数量不同你可以将不同的参数设置为可选
```ts
function fn(params: number): void
function fn(params: string, params2: number): void
function fn(params: any, params2?: any): void {
  console.log(params)
  console.log(params2)
}
fn(123)
fn('123',456)
```

## 类型断言|联合类型|交叉类型
### 联合类型
```ts
//例如我们的手机号通常是13XXXXXXX 为数字类型 这时候产品说需要支持座机
//所以我们就可以使用联合类型支持座机字符串
let myPhone: number | string  = '010-820'
 
//这样写是会报错的应为我们的联合类型只有数字和字符串并没有布尔值
let myPhone: number | string  = true
```
**函数使用联合类型**
```ts
const fn = (something:number | boolean):boolean => {
  return !!something
}
```

### 交叉类型
>多种类型的集合，联合对象将具有所联合类型的所有成员
```ts
interface People {
  age: number,
  height： number
}
interface Man{
  sex: string
}
const xiaoman = (man: People & Man) => {
  console.log(man.age)
  console.log(man.height)
  console.log(man.sex)
}
xiaoman({age: 18,height: 180,sex: 'male'});
```

### 类型断言
>语法：**值 as 类型(value as string)**　或 **<类型>值(<string>value)** 
```ts
interface A {
  run: string
}
interface B {
  build: string
}
const fn = (type: A | B): string => {
  return type.run
}
//这样写是有警告的应为B的接口上面是没有定义run这个属性的

//修改为：
const fn = (type: A | B): string => {
  return (type as A).run
  //第二种写法
  return (<type>A).run;
}
//可以使用类型断言来推断他传入的是A接口的值
```
需要注意的是，类型断言只能够「欺骗」TypeScript 编译器，无法避免运行时的错误，反而滥用类型断言可能会导致运行时错误:
**使用any临时断言**
```ts
window.abc = 123
//这样写会报错因为window没有abc这个东西

(window as any).abc = 123
//可以使用any临时断言在 any 类型的变量上，访问任何属性都是允许的。
```

### as const
>是对字面值的断言，与const直接定义常量是有区别的
>如果是普通类型跟直接const 声明是一样的
```ts
const names = 'test'
names = 'aa' //无法修改
let names2 = 'test' as const
names2 = 'aa' //无法修改

// 数组
let a1 = [10, 20] as const;
const a2 = [10, 20];
 
a1.unshift(30); // 错误，此时已经断言字面量为[10, 20],数据无法做任何修改
a2.unshift(30); // 通过，没有修改指针
```

### 类型断言是不具影响力的
>在下面的例子中，将 something 断言为 boolean 虽然可以通过编译，但是并没有什么用 并不会影响结果, 因为编译过程中会删除类型断言
```ts
function toBoolean(something: any): boolean {
  return something as boolean;
}
toBoolean(1);
// 返回值为 1
```

## 内置对象
>JS中有很多的内置对象，他们可以直接在TS中当作定义好的类型。
### ECMAScript内置对象
**Boolean、Number、String、RegExp、Date、Error**
```ts
let b: Boolean = new Boolean(1)
console.log(b)
let n: Number = new Number(true)
console.log(n)
let s: String = new String('zs')
console.log(s)
let d: Date = new Date()
console.log(d)
let r: RegExp = /^1/
console.log(r)
let e: Error = new Error("error!")
console.log(e)
```

### DOM和BOM的内置对象
Document、HTMLElement、Event、NodeList等
```ts
let body: HTMLElement = document.body;
let allDiv: NodeList = document.querySelectorAll('div');
//读取div 这种需要类型断言 或者加个判断应为读不到返回null
let div:HTMLElement = document.querySelector('div') as HTMLDivElement
document.addEventListener('click', function (e: MouseEvent) {
    
});
//dom元素的映射表
interface HTMLElementTagNameMap {
  "a": HTMLAnchorElement;
  "abbr": HTMLElement;
  "address": HTMLElement;
  "applet": HTMLAppletElement;
  "area": HTMLAreaElement;
  "article": HTMLElement;
  "aside": HTMLElement;
  "audio": HTMLAudioElement;
  "b": HTMLElement;
  "base": HTMLBaseElement;
  "bdi": HTMLElement;
  "bdo": HTMLElement;
  "blockquote": HTMLQuoteElement;
  "body": HTMLBodyElement;
  "br": HTMLBRElement;
  "button": HTMLButtonElement;
  "canvas": HTMLCanvasElement;
  "caption": HTMLTableCaptionElement;
  "cite": HTMLElement;
  "code": HTMLElement;
  "col": HTMLTableColElement;
  "colgroup": HTMLTableColElement;
  "data": HTMLDataElement;
  "datalist": HTMLDataListElement;
  "dd": HTMLElement;
  "del": HTMLModElement;
  "details": HTMLDetailsElement;
  "dfn": HTMLElement;
  "dialog": HTMLDialogElement;
  "dir": HTMLDirectoryElement;
  "div": HTMLDivElement;
  "dl": HTMLDListElement;
  "dt": HTMLElement;
  "em": HTMLElement;
  "embed": HTMLEmbedElement;
  "fieldset": HTMLFieldSetElement;
  "figcaption": HTMLElement;
  "figure": HTMLElement;
  "font": HTMLFontElement;
  "footer": HTMLElement;
  "form": HTMLFormElement;
  "frame": HTMLFrameElement;
  "frameset": HTMLFrameSetElement;
  "h1": HTMLHeadingElement;
  "h2": HTMLHeadingElement;
  "h3": HTMLHeadingElement;
  "h4": HTMLHeadingElement;
  "h5": HTMLHeadingElement;
  "h6": HTMLHeadingElement;
  "head": HTMLHeadElement;
  "header": HTMLElement;
  "hgroup": HTMLElement;
  "hr": HTMLHRElement;
  "html": HTMLHtmlElement;
  "i": HTMLElement;
  "iframe": HTMLIFrameElement;
  "img": HTMLImageElement;
  "input": HTMLInputElement;
  "ins": HTMLModElement;
  "kbd": HTMLElement;
  "label": HTMLLabelElement;
  "legend": HTMLLegendElement;
  "li": HTMLLIElement;
  "link": HTMLLinkElement;
  "main": HTMLElement;
  "map": HTMLMapElement;
  "mark": HTMLElement;
  "marquee": HTMLMarqueeElement;
  "menu": HTMLMenuElement;
  "meta": HTMLMetaElement;
  "meter": HTMLMeterElement;
  "nav": HTMLElement;
  "noscript": HTMLElement;
  "object": HTMLObjectElement;
  "ol": HTMLOListElement;
  "optgroup": HTMLOptGroupElement;
  "option": HTMLOptionElement;
  "output": HTMLOutputElement;
  "p": HTMLParagraphElement;
  "param": HTMLParamElement;
  "picture": HTMLPictureElement;
  "pre": HTMLPreElement;
  "progress": HTMLProgressElement;
  "q": HTMLQuoteElement;
  "rp": HTMLElement;
  "rt": HTMLElement;
  "ruby": HTMLElement;
  "s": HTMLElement;
  "samp": HTMLElement;
  "script": HTMLScriptElement;
  "section": HTMLElement;
  "select": HTMLSelectElement;
  "slot": HTMLSlotElement;
  "small": HTMLElement;
  "source": HTMLSourceElement;
  "span": HTMLSpanElement;
  "strong": HTMLElement;
  "style": HTMLStyleElement;
  "sub": HTMLElement;
  "summary": HTMLElement;
  "sup": HTMLElement;
  "table": HTMLTableElement;
  "tbody": HTMLTableSectionElement;
  "td": HTMLTableDataCellElement;
  "template": HTMLTemplateElement;
  "textarea": HTMLTextAreaElement;
  "tfoot": HTMLTableSectionElement;
  "th": HTMLTableHeaderCellElement;
  "thead": HTMLTableSectionElement;
  "time": HTMLTimeElement;
  "title": HTMLTitleElement;
  "tr": HTMLTableRowElement;
  "track": HTMLTrackElement;
  "u": HTMLElement;
  "ul": HTMLUListElement;
  "var": HTMLElement;
  "video": HTMLVideoElement;
  "wbr": HTMLElement;
}
```

### 定义Promise
如果我们不指定返回的类型TS是推断不出来返回的是什么类型
```ts
function PromiseFunc() {
  return new Promise((resolve, reject) => {
    resolve(1);
  })
}
```
**这样定义，TS是不知道PromiseFunc返回的类型是什么`Promise<unknown>`**
所以，我们需要指定返回的类型
```ts
function promise():Promise<number>{
   return new Promise<number>((resolve,reject)=>{ resolve(1) })
}
```

## Class类
### 定义类
>在TypeScript是不允许直接在constructor 定义变量的 需要在constructor上面先声明
>如果了定义了变量不用 也会报错 通常是给个默认值 或者 进行赋值
```ts
class Person {
  name: string
  age: number
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
```

### 类的修饰符
- public: 可以让你定义的变量，内部访问，也可以外部访问，不写默认就是public
- private: 只能内部访问，子类不能访问到，外部也不能
- protected：只能内部访问，子类可以访问到，外部不能访问

### static静态属性和静态方法
>static 定义的属性 不可以通过this 去访问 只能通过类名去调用
```ts
class Person {
  static nb: string
  constructor() {
    // this.nb this访问不到
  }
}
Person.nb
```
>static 静态函数 同样也是不能通过this 去调用 也是通过类名去调用
>需注意： **如果两个函数都是static 静态的是可以通过this互相调用**

### interface 定义 类
> ts interface 定义类 使用关键字 implements   后面跟interface的名字多个用逗号隔开
>继承还是用extends
```ts
interface PersonClass {
    get(type: boolean): boolean
}
 
interface PersonClass2{
    set():void,
    asd:string
}
 
class A {
    name: string
    constructor() {
        this.name = "123"
    }
}
class Person extends A implements PersonClass,PersonClass2 {
    asd: string
    constructor() {
        super()
        this.asd = '123'
    }
    get(type:boolean) {
        return type
    }
    set () {
 
    }
}
```

### 抽象类
>应用场景如果你写的类实例化之后毫无用处此时我可以把他定义为抽象类
>或者你也可以把他作为一个基类-> 通过继承一个派生类去实现基类的一些方法
下面这段代码会报错抽象类无法被实例化
```ts
abstract class A {
  public name:string
}
new A()
```

例子2: 我们在A类定义了 getName 抽象方法但未实现
我们B类实现了A定义的抽象方法 如不实现就不报错 我们定义的抽象方法必须在派生类实现
```ts
abstract class A {
   name: string
   constructor(name: string) {
      this.name = name;
   }
   print(): string {
      return this.name
   }
   abstract getName(): string
}
class B extends A {
   constructor() {
      super('小满')
   }
   getName(): string {
      return this.name
   }
}
let b = new B();
console.log(b.getName());
```

## 元组类型
如果需要一个固定大小的不同类型的集合，我们需要使用元组
### 元组就是数组的变种
>元组（Tuple）是固定数量的不同类型的元素的组合
元组与集合的不同之处在于，元组中的元素类型可以是不同的，而且数量固定。元组的好处在于可以把多个元素作为一个单元传递。如果一个方法需要返回多个值，可以把这多个值作为元组返回，而不需要创建额外的类来表示。
```ts
let arr:[number,string] = [1,'string']
let arr2: readonly [number,boolean,string,undefined] = [1,true,'sring',undefined]
```
当赋值或访问一个已知索引时，会得到正确的类型

### 越界元素
```ts
let arr:[number,string] = [1,'string']
arr.push(true)//error
```
**对于越界的元素他的类型被限制为 联合类型（就是你在元组中定义的类型）**

### 应用场景：例如定义Excel返回的数据
```ts
let excel: [string, string, number, string][] = [
    ['title', 'name', 1, '123'],
    ['title', 'name', 1, '123'],
    ['title', 'name', 1, '123'],
    ['title', 'name', 1, '123'],
    ['title', 'name', 1, '123'],
]
```

## 枚举类型
在javaScript中是没有枚举的概念的TS帮我们定义了枚举这个类型,**通过enum关键字定义枚举**
### 数字枚举
例如 红绿蓝 Red = 0 Green = 1 Blue= 2 分别代表红色0 绿色为1 蓝色为2
```ts
enum Types{
  Red,
  Green,
  BLue
}
```
这样写就可以实现应为ts定义的枚举中的每一个组员默认都是从0开始的所以也就是
```ts
enum Types {
  Red = 0,
  Green = 1, 
  Blur = 2
}
```
增长枚举
```ts
enum Types{
   Red = 1,
   Green,
   BLue
}
```
如上，我们定义了一个数字枚举， Red使用初始化为 1。 其余的成员会从 1开始自动增长。 换句话说， Type.Red的值为 1， Green为 2， Blue为 3。


### 字符串枚举
字符串枚举的概念很简单。 在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化。
```ts
enum Types{
   Red = 'red',
   Green = 'green',
   BLue = 'blue'
}
```
由于字符串枚举没有自增长的行为，字符串枚举可以很好的序列化。

### 异构枚举
>枚举可以混合字符串和数字成员
```ts
enum Types{
  No = "No",
  Yes = 1,
}
```

### 接口枚举
```ts
   enum Types {
      yyds,
      dddd
   }
   interface A {
      red:Types.yyds
   }
 
   let obj:A = {
      red:Types.yyds
   }
```

### const枚举
let  和 var 都是不允许的声明只能使用const
大多数情况下，枚举是十分有效的方案。 然而在某些情况下需求很严格。 为了避免在额外生成的代码上的开销和额外的非直接的对枚举成员的访问，我们可以使用 const枚举。 常量枚举通过在枚举上使用 const修饰符来定义
const 声明的枚举会被编译成常量
普通声明的枚举编译完后是个对象
```ts
const enum Types{
   No = "No",
   Yes = 1,
}
```
const编译之后：`console.log(12 /* Blue */);`

### 反向映射
它包含了正向映射（ name -> value）和反向映射（ value -> name）
**要注意的是 不会为字符串枚举成员生成反向映射**
```ts
enum Enum {
   fall
}
let a = Enum.fall;
console.log(a); //0
let nameOfA = Enum[a]; 
console.log(nameOfA); //fall
```

## 类型推论|类型别名
### 什么是类型推论
`let str = "ceshi"`
- 我声明了一个变量但是没有定义类型
**TypeScript 会在没有明确的指定类型的时候推测出一个类型，这就是类型推论**,不能够在赋值给别的类型
- 如果你声明变量没有定义类型也没有赋值这时候TS会推断成any类型可以进行任何操作

### 类型别名
>type关键字（可以给一个类型定义一个名字）==>多用于符合类型
- 定义类型别名
```ts
type str = string;
let s: str = 'ces';
```
- 定义函数别名
```ts
type str = () => string
let s: str = () => 'aaa';
```
- 定义联合类型别名
```ts
type str = string | number;
let s: str = 123
str = '555'
```
- 定义值的别名
```ts
type value = boolean | 0 | '213'
let s: value = true;
```

## never类型
>TypeScript 将使用 never 类型来表示不应该存在的状态(很抽象是不是)
```ts
// 返回never的函数必须存在无法达到的终点
// 因为必定抛出异常，所以 error 将不会有返回值
function error(message: string): never {
    throw new Error(message);
}
// 因为存在死循环，所以 loop 将不会有返回值
function loop(): never {
    while (true) {
    }
}
```

### never和void差异
```ts
//void类型只是没有返回值 但本身不会出错
function Void():void {
  console.log();
}
//只会抛出异常没有返回值
function Never():never {
  throw new Error('aaa')
}
```
**never 类型的一个应用场景**
```ts
interface A {
    type: "foo"
}
 
interface B {
    type: "bar"
}
type All = A | B ;
function handleValue(val: All) {
  switch (val.type) {
    case 'foo':
      break;
    case 'bar':
      break
    default:
      //兜底逻辑 一般是不会进入这儿如果进来了就是程序异常了
      const exhaustiveCheck:never = val;
      break
  }
}
```
比如新来了一个同事他新增了一个C接口，我们必须手动找到所有 switch 代码并处理，否则将有可能引入 BUG 。
而且这将是一个“隐蔽型”的BUG，如果回归面不够广，很难发现此类BUG。
**由于任何类型都不能赋值给 never 类型的变量，所以当存在进入 default 分支的可能性时，TS的类型检查会及时帮我们发现这个问题**

## symbol类型
>symbol类型的值是通过Symbol构造函数创建的
可以传递参做为唯一标识 只支持 string 和 number类型的参数
```ts
let sym1 = Symbol();
let sym2 = Symbol("key"); // 可选的字符串key
```
### Symbol的值是唯一的
```ts
const s1 = Symbol()
const s2 = Symbol()
// s1 === s2 =>false
```
### 用作对象属性的键
```ts
let sym = Symbol();
let obj = {
  [sym]: "value"
};
console.log(obj[sym]); // "value"
```
### 使用symbol定义的属性，是不能通过如下方式遍历拿到的
```ts
const symbol1 = Symbol('666')
const symbol2 = Symbol('777')
const obj1= {
   [symbol1]: '小满',
   [symbol2]: '二蛋',
   age: 19,
   sex: '女'
}
// 1 for in 遍历
for (const key in obj1) {
   // 注意在console看key,是不是没有遍历到symbol1
   console.log(key)
}
// 2 Object.keys 遍历
Object.keys(obj1)
console.log(Object.keys(obj1))
// 3 getOwnPropertyNames
console.log(Object.getOwnPropertyNames(obj1))
// 4 JSON.stringfy
console.log(JSON.stringify(obj1))
```
**如何拿到**
```ts
// 1 拿到具体的symbol 属性,对象中有几个就会拿到几个
Object.getOwnPropertySymbols(obj1)
console.log(Object.getOwnPropertySymbols(obj1))
// 2 es6 的 Reflect 拿到对象的所有属性
Reflect.ownKeys(obj1)
console.log(Reflect.ownKeys(obj1))
```

### Symbol.iterator迭代器和生成器for of
>支持遍历大部分类型迭代器 arr nodeList argumetns set map 等
```ts
var arr = [1,2,3,4];
let iterator = arr[Symbol.iterator]();
console.log(iterator.next());  //{ value: 1, done: false }
console.log(iterator.next());  //{ value: 2, done: false }
console.log(iterator.next());  //{ value: 3, done: false }
console.log(iterator.next());  //{ value: 4, done: false }
console.log(iterator.next());  //{ value: undefined, done: true }
```
测试用例
```ts
interface Item {
    age: number,
    name: string
}
 
const array: Array<Item> = [{ age: 123, name: "1" }, { age: 123, name: "2" }, { age: 123, name: "3" }]
// let it:Iterator<Item> = array[Symbol.iterator]()
 
type mapTypes = string | number
const map:Map<mapTypes,mapTypes> = new Map()
 
map.set('1','王爷')
map.set('2','陆北')

let it2: Iterator<Array<mapTypes>> = map[Symbol.iterator]();

const obj = {
    aaa:123,
    bbb:456
}
 
let set:Set<number> = new Set([1,2,3,4,5,6])
let it3: Iterator<number> = set[Symbol.iterator]();

const gen = (erg:any): void => {
    let it: Iterator<any> = erg[Symbol.iterator]()
    let next:any= { done: false }
    while (!next.done) {
        next =  it.next()
        if (!next.done) {
            console.log(next.value)
        }
    }
}
gen(array)
```

### symbols列表
`Symbol.hasInstance`
方法，会被instanceof运算符调用。构造器对象用来识别一个对象是否是其实例。
`Symbol.isConcatSpreadable`
布尔值，表示当在一个对象上调用Array.prototype.concat时，这个对象的数组元素是否可展开。
`Symbol.iterator`
方法，被for-of语句调用。返回对象的默认迭代器。
`Symbol.match`
方法，被String.prototype.match调用。正则表达式用来匹配字符串。
`Symbol.replace`
方法，被String.prototype.replace调用。正则表达式用来替换字符串中匹配的子串。
`Symbol.search`
方法，被String.prototype.search调用。正则表达式返回被匹配部分在字符串中的索引。
`Symbol.species`
函数值，为一个构造函数。用来创建派生对象。
`Symbol.split`
方法，被String.prototype.split调用。正则表达式来用分割字符串。
`Symbol.toPrimitive`
方法，被ToPrimitive抽象操作调用。把对象转换为相应的原始值。
`Symbol.toStringTag`
方法，被内置方法Object.prototype.toString调用。返回创建对象时默认的字符串描述。
`Symbol.unscopables`
对象，它自己拥有的属性会被with作用域排除在外。

## 泛型
### 函数泛型
```ts
function num (a:number,b:number) : Array<number> {
    return [a ,b];
}
num(1,2)
function str (a:string,b:string) : Array<string> {
    return [a ,b];
}
str('独孤','求败')
```
**泛型优化**
语法为函数名字后面跟一个<参数名> 参数名可以随便写 例如我这儿写了T
当我们使用这个函数的时候把参数的类型传进去就可以了 （也就是动态类型）
```ts
function Add<T>(a: T, b: T): Array<T> {
  return [a, b];
}
Add<number>(1, 2);
Add<number>('1', '2');
```
**我们也可以使用不同的泛型参数名，只要在数量上和使用方式上能对应上就可以**
```ts
function Sub<T,U>(a:T,b:U):Array<T|U> {
    const params:Array<T|U> = [a,b]
    return params
}
Sub<Boolean,number>(false,1)
```

### 定义泛型接口
声明接口的时候，在名字后面加一个<参数>
使用的时候传递类型
```ts
interface MyInter<T> {
   (arg: T): T
}
function fn<T>(arg: T): T {
   return arg
}
let result: MyInter<number> = fn
 
result(123)
```

### 对象字面量泛型
```ts
let foo: { <T>(arg: T): T }
foo = function <T>(arg:T):T {
   return arg
}
foo(123)
```

### 泛型约束
>我们期望在一个泛型的变量上面，获取其length参数，但是，有的数据类型是没有length属性的
```ts
function getLegnth<T>(arg:T) {
  return arg.length;//报错，类型T上不存在属性“length”
}
```
这时候我们就可以使用泛型约束
于是，我们就得对使用的泛型约束，我们约束其为具有length属性的类型，这里我们会用到interface,代码如下
```ts
interface Len {
   length:number
}
function getLegnth<T extends Len>(arg:T) {
  return arg.length
}
getLegnth<string>('123')
```

### 使用keyof约束对象
其中使用了TS泛型和泛型约束。首先定义了T类型并使用extends关键字继承object类型的子类型，然后使用keyof操作符获取T类型的所有键，它的返回 类型是联合 类型，最后利用extends关键字约束 K类型必须为keyof T联合类型的子类型
```ts
function prop<T, K extends keyof T>(obj: T, key: K) {
   return obj[key]
}
let o = { a: 1, b: 2, c: 3 }
prop(o, 'a') 
prop(o, 'd') 
```

### 泛型类
>声明方法跟函数类似名称后面定义<类型>
>使用的时候确定类型`new Sub<number>()`
```ts
class Sub<T>{
   attr: T[] = [];
   add (a:T):T[] {
      return [a]
   }
}
let s = new Sub<number>()
s.attr = [1,2,3]
s.add(123)
let str = new Sub<string>()
str.attr = ['1','2','3']
str.add('123')
```

## tsconfig.json配置文件
```json
"compilerOptions": {
  "incremental": true, // TS编译器在第一次编译之后会生成一个存储编译信息的文件，第二次编译会在第一次的基础上进行增量编译，可以提高编译的速度
  "tsBuildInfoFile": "./buildFile", // 增量编译文件的存储位置
  "diagnostics": true, // 打印诊断信息 
  "target": "ES5", // 目标语言的版本
  "module": "CommonJS", // 生成代码的模板标准
  "outFile": "./app.js", // 将多个相互依赖的文件生成一个文件，可以用在AMD模块中，即开启时应设置"module": "AMD",
  "lib": ["DOM", "ES2015", "ScriptHost", "ES2019.Array"], // TS需要引用的库，即声明文件，es5 默认引用dom、es5、scripthost,如需要使用es的高级版本特性，通常都需要配置，如es8的数组新特性需要引入"ES2019.Array",
  "allowJS": true, // 允许编译器编译JS，JSX文件
  "checkJs": true, // 允许在JS文件中报错，通常与allowJS一起使用
  "outDir": "./dist", // 指定输出目录
  "rootDir": "./", // 指定输出文件目录(用于输出)，用于控制输出目录结构
  "declaration": true, // 生成声明文件，开启后会自动生成声明文件
  "declarationDir": "./file", // 指定生成声明文件存放目录
  "emitDeclarationOnly": true, // 只生成声明文件，而不会生成js文件
  "sourceMap": true, // 生成目标文件的sourceMap文件
  "inlineSourceMap": true, // 生成目标文件的inline SourceMap，inline SourceMap会包含在生成的js文件中
  "declarationMap": true, // 为声明文件生成sourceMap
  "typeRoots": [], // 声明文件目录，默认时node_modules/@types
  "types": [], // 加载的声明文件包
  "removeComments":true, // 删除注释 
  "noEmit": true, // 不输出文件,即编译后不会生成任何js文件
  "noEmitOnError": true, // 发送错误时不输出任何文件
  "noEmitHelpers": true, // 不生成helper函数，减小体积，需要额外安装，常配合importHelpers一起使用
  "importHelpers": true, // 通过tslib引入helper函数，文件必须是模块
  "downlevelIteration": true, // 降级遍历器实现，如果目标源是es3/5，那么遍历器会有降级的实现
  "strict": true, // 开启所有严格的类型检查
  "alwaysStrict": true, // 在代码中注入'use strict'
  "noImplicitAny": true, // 不允许隐式的any类型
  "strictNullChecks": true, // 不允许把null、undefined赋值给其他类型的变量
  "strictFunctionTypes": true, // 不允许函数参数双向协变
  "strictPropertyInitialization": true, // 类的实例属性必须初始化
  "strictBindCallApply": true, // 严格的bind/call/apply检查
  "noImplicitThis": true, // 不允许this有隐式的any类型
  "noUnusedLocals": true, // 检查只声明、未使用的局部变量(只提示不报错)
  "noUnusedParameters": true, // 检查未使用的函数参数(只提示不报错)
  "noFallthroughCasesInSwitch": true, // 防止switch语句贯穿(即如果没有break语句后面不会执行)
  "noImplicitReturns": true, //每个分支都会有返回值
  "esModuleInterop": true, // 允许export=导出，由import from 导入
  "allowUmdGlobalAccess": true, // 允许在模块中全局变量的方式访问umd模块
  "moduleResolution": "node", // 模块解析策略，ts默认用node的解析策略，即相对的方式导入
  "baseUrl": "./", // 解析非相对模块的基地址，默认是当前目录
  "paths": { // 路径映射，相对于baseUrl
    // 如使用jq时不想使用默认版本，而需要手动指定版本，可进行如下配置
    "jquery": ["node_modules/jquery/dist/jquery.min.js"]
  },
  "rootDirs": ["src","out"], // 将多个目录放在一个虚拟目录下，用于运行时，即编译后引入文件的位置可能发生变化，这也设置可以虚拟src和out在同一个目录下，不用再去改变路径也不会报错
  "listEmittedFiles": true, // 打印输出文件
  "listFiles": true// 打印编译的文件(包括引用的声明文件)
}
 
// 指定一个匹配列表（属于自动指定该路径下的所有ts相关文件）
"include": [
   "src/**/*"
],
// 指定一个排除列表（include的反向操作）
 "exclude": [
   "demo.ts"
],
// 指定哪些文件使用该配置（属于手动一个个指定文件）
 "files": [
   "demo.ts"
]
```
>常用：
- include: 指定编译文件默认是编译当前目录下所有的ts文件
- exclude: 指定排除的文件
- target: 指定编译js的版本
- allowJS: 是否允许编译js文件
- removeComments: 是否在编译过程中删除文件中的注释
- rootDir: 编译文件的目录
- outDir: 输出的目录
- sourceMap: 代码源文件
- strict: 严格模式
- module: 默认commonJS,可选es模式、amd、umd等

## namescpace命名空间
>我们在工作中无法避免全局变量造成的污染，TypeScript提供了namespace 避免这个问题出现
- 内部模块，主要用于组织代码，避免命名冲突。
- 命名空间内的类默认私有
- 通过 export 暴露
- 通过 namespace 关键字定义

**TypeScript与ECMAScript 2015一样，任何包含顶级import或者export的文件都被当成一个模块。相反地，如果一个文件不带有顶级的import或者export声明，那么它的内容被视为全局可见的（因此对模块也是可见的）**

命名空间中通过export将想要暴露的部分导出
如果不用export 导出是无法读取其值的
```ts
namespace a2 {
  export const Time: number = 1000;
  export const fn = <T>(arg: T): T[] => {
    return [arg];
  }
}
console.log(a2.Time, a2.fn<number>(a2.Time));
```

### 嵌套命名空间
```ts
namespace a {
  export namespace b {
    export class Vue {
      parameters: string
      constructor(parameters: string) {
        this.parameters = parameters
      }
    }
  }
}
let v = a.b.Vue
new v('1')
```

### 抽离命名空间
```ts
//a.ts
export namespace V {
  export const a = 1;
}
//b.ts
import { V } from '../observer/index';
console.log(V);//{a: 1}
```

### 简化命名空间
```ts
namespace A  {
  export namespace B {
    export const C = 1
  }
}
import X = A.B.C
console.log(X);
```

### 合并命名空间
>重名的命名空间会合并

## 三斜线指令
>三斜线指令是包含单个XML标签的单行注释。 注释的内容会做为编译器指令使用。
>三斜线指令仅可放在包含它的文件的最顶端。 一个三斜线指令的前面只能出现单行或多行注释，这包括其它的三斜线指令。 如果它们出现在一个语句或声明之后，那么它们会被当做普通的单行注释，并且不具有特殊的涵义。
>`/// <reference path="..." />`指令是三斜线指令中最常见的一种。 它用于声明文件间的 依赖。 =>三斜线引用告诉编译器在编译过程中要引入的额外的文件
你也可以把它理解能import，它可以告诉编译器在编译过程中要引入的额外的文件

例如：
```ts
//a.ts
namespace A {
  export const fn = () => 'a'
}
//b.ts
//引入之后直接可以使用变量A
///<reference path="./index2.ts" />
console.log(A);
```

### 声明文件引入
例如，把 `/// <reference types="node" />`引入到声明文件，表明这个文件使用了 `@types/node/index.d.ts`里面声明的名字； 并且，这个包需要在编译阶段与声明文件一起被包含进来。
仅当在你需要写一个d.ts文件时才使用这个指令
```ts
///<reference types="node" />
```

**注意事项：**
如果你在配置文件 配置了`noResolve` 或者自身调用自身文件会报错

## 声明文件d.ts
### 声明文件declare
>当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码补全、接口提示等功能
```ts
declare var //声明全局变量
declare function //声明全局方法
declare class //声明全局类
declare enum //声明全局枚举类型
declare namespace //声明（含有子属性的）全局对象
interface 和 type //声明全局类型
/// <reference /> 三斜线指令
```

- 例如我们有一个express 和 axios
发现express 报错了
让我们去下载他的声明文件
`npm install @types/node -D`
那为什么axios 没有报错
我们可以去`node_modules` 下面去找axios 的`package json`
发现axios已经指定了声明文件 所以没有报错可以直接用
通过语法declare 暴露我们声明的axios 对象
`declare  const axios: AxiosStatic;`
如果有一些第三方包确实没有声明文件我们可以自己去定义
名称.d.ts 创建一个文件去声明

## Mixins混入
TS混入Mixins，可以将它看成合并
### 对象混入
可以使用es6的Object.assign合并多个对象
```ts
interface Name {
  name: string
}
interface Age {
  age: number
}
interface Sex {
  sex: number
}
let people1: Name = { name: "小满" }
let people2: Age = { age: 20 }
let people3: Sex = { sex: 1 }
const people = Object.assign(people1,people2,people3)
```
**此时 people 会被推断成一个交叉类型 `Name & Age & sex;`**

### 类的混入
首先声明两个mixins类 （严格模式要关闭不然编译不过）
```ts
class A {
  type: boolean = false;
  changeType() {
    this.type = !this.type
  }
}
class B {
  name: string = '张三';
  getName(): string {
    return this.name;
  }
}
```
下面创建一个类，结合了这两个mixins
首先应该注意到的是，没使用extends而是使用`implements`。 把类当成了接口
我们可以这么做来达到目的，为将要mixin进来的属性方法创建出占位属性。 这告诉编译器这些成员在运行时是可用的。 这样就能使用mixin带来的便利，虽说需要提前定义一些占位属性
```ts
class C implements A,B{
    type:boolean
    changeType:()=>void;
    name: string;
    getName:()=> string
}
```
最后，创建这个帮助函数，帮我们做混入操作。 它会遍历mixins上的所有属性，并复制到目标上去，把之前的占位属性替换成真正的实现代码
**`Object.getOwnPropertyNames()`可以获取对象自身的属性，除去他继承来的属性，对它所有的属性遍历，它是一个数组，遍历一下它所有的属性名**
```ts
Mixins(C, [A, B])
function Mixins(curCls: any, itemCls: any[]) {
    itemCls.forEach(item => {
        Object.getOwnPropertyNames(item.prototype).forEach(name => {
            curCls.prototype[name] = item.prototype[name]
        })
    })
}
```

## 装饰器Decorator
>**Decorator装饰器是一项实验性特性，在未来的版本中可能会发生改变**
它们不仅增加了代码的可读性，清晰地表达了意图，而且提供一种方便的手段，增加或修改类的功能
若要启用实验性的装饰器特性，你必须在命令行或tsconfig.json里启用编译器选项
```json
{
  "experimentalDecorators": true,
}
```
### 装饰器
装饰器是一种特殊类型的声明，它能够被附加到类声明，方法，访问符，属性或参数上
```ts
//定义一个类装饰器函数 他会把Class A的构造函数传入你的watcher函数当做第一个参数
const watcher: ClassDecorator = (target: Function) => {
    target.prototype.getParams = <T>(params: T):T => {
        return params
    }
}

@watcher
class A {
    constructor() {
    }
}

const a = new A();
console.log((a as any).getParams('123'));
```

### 装饰器工厂函数
**其实也就是一个高阶函数 外层的函数接受值 里层的函数最终接受类的构造函数**
```ts
const watcher = (name: string): ClassDecorator => {
    return (target: Function) => {
        target.prototype.getParams = <T>(params: T): T => {
            return params
        }
        target.prototype.getOptions = (): string => {
            return name
        }
    }
}
 
@watcher('name')
class A {
    constructor() {
 
    }
}
 
const a = new A();
console.log((a as any).getParams('123'));
```

### 装饰器组合
>就是可以使用多个装饰器
### 方法装饰器
返回三个参数:
- 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 成员的名字。
- 成员的属性描述符。
```ts
const met:MethodDecorator = (...args) => {
  console.log(args);
}
class A {
  constructor() {}
  @met
  getName ():string {
    return '小满'
  }
}
const a = new A();
```

### 属性装饰器
返回两个参数
- 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 属性的名字
```ts
const met:PropertyDecorator = (...args) => {
    console.log(args);
}
class A {
    @met
    name:string
    constructor() {
    }
}
const a = new A();
```
### 参数装饰器
返回三个参数
- 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 成员的名字。
- 参数在函数参数列表中的索引
```ts
const met:ParameterDecorator = (...args) => {
    console.log(args);
}
class A {
    constructor() {
    }
    setParasm (@met name:string = '213') {
    }
}
const a = new A();
```

## Rollup构建TS项目 & webpack构建TS项目

## 实战TS编写发布订阅模式
>发布订阅模式例如`addEventListener`，`Vue evnetBus`都属于发布订阅模式
首先 需要定义三个角色 发布者 订阅者 调度者
- on订阅/监听
- emit 发布/注册
- once 只执行一次
- off解除绑定
```ts
interface EventFace {
  on: (name: string, callback: Function) => void,
  emit: (name: string, ...args: Array<any>) => void,
  off: (name: string, fn: Function) => void,
  once: (name: string, fn: Function) => void
}
interface List {
  [key: string]: Array<Function>
}
class Dispatch implements EventFace {
  list: List
  constructor() {
    this.list = {};
  }
  on(name: string, callback: Function): void {
    const callbackList: Array<Function> = this.list[name] || [];
    callbackList.push(callback);
    this.list[name] = callbackList;
  },
  emit(name: string, fn: Function): void {
    let eventName: Array<Function> = this.list[name];
    if (eventName) {
      eventName.forEach(eventFn => {
        eventFn.apply(this, args);
      })
    } else {
      console.error('该事件未监听');
    }
  },
  off(name: string, fn: Function): void {
    let eventName: Array<Function> = this.list[name];
    if (eventName && fn) {
      let index: number = eventName.findIndex(fns => fns === fn);
      eventName.splice(index, 1);
    } else {
      console.error('该事件未监听');
    }
  },
  once(name: string, fn: Function): void {
    let dector = (...args) => {
      fn.apply(this, args);
      this.off(name, dector);
    }
    this.on(name, dector);
  }
}
```

## TS进阶用法Proxy & Reflect
### Proxy
>Proxy对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）
**target**
要使用Proxy包装的目标对象（可以是任何类型的对象，包括元素数组，函数，甚至另一个代理）
**handler**
一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理p的行为
- handler.get()本次使用的get => 属性读取操作的捕捉器
- handler.set()本次使用的set => 属性设置操作的捕捉器

### Reflect
>与大多数全局对象不同Reflect并非一个构造函数，所以不能通过new 运算符对其进行调用，或者将Reflect对象作为一个函数来调用。Reflect的所有属性和方法都是静态的
**Reflect.get(target, name, receiver)**
Reflect.get方法查找并返回target对象的name属性，如果没有该属性返回undefined
**Reflect.set(target, name, value, receiver)**
Reflect.set方法设置target对象的name属性等于value.

```ts
type Person = {
  name: string,
  age: number,
  text: string
}
const proxy = (object: any, key: any) => {
    return new Proxy(object, {
        get(target, prop, receiver) {
            console.log(`get key======>${key}`);
            return Reflect.get(target, prop, receiver)
        },
        set(target, prop, value, receiver) {
            console.log(`set key======>${key}`);
            return Reflect.set(target, prop, value, receiver)
        }
    })
}
const logAccess = (object: Person, key: 'name' | 'age' | 'text') => {
    return proxy(object, key)
}
let man: Person = logAccess({
    name: "小满",
    age: 20,
    text: "我的很小"
}, 'age')
man.age  = 30
console.log(man);
```
**使用泛型+keyof优化**
```ts
const logAccess = <T>(object: T, key: keyof T): T => {
  return proxy(object, key);
}
```

## TS进阶用法Partial & Pick
### Partial
>TS内置高级类型Partial Pick
**看一下源码**
```ts
/**
 * Make all properties in T optional
  将T中的所有属性设置为可选
 */
type Partial<T> = {
  [P in keyof T] ?: T[P]
}
```
>- keyof：将一个接口对象的全部属性提取出来变成联合类型
>- in: 我们可以理解成for in，遍历keyof T就是遍历联合类型的每一项
>- ?：将一个属性变成可选属性
>- T[P]: 索引访问操作符，与JS中访问属性值的操作类似
使用前
```ts
type Person = {
  name:string,
  age:number
}
type p = Partial<Person>
```
转换后全部转为了可选
```ts
type p = {
  name?: string | undefined;
  age?: number | undefined;
}
```

### Pick
>从类型定义T的属性中，选取指定一组属性，返回一个新的类型定义。
```ts
/**
 * From T, pick a set of properties whose keys are in the union K
 */
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};
```
```ts
type Person = {
  name:string,
  age:number,
  text:string
  address:string
}
type Ex = "text" | "age"
type A = Pick<Person,Ex>
```

## TS进阶用法Record & Readonly
### Readonly
>和Partial相似，只是将?操作符替换成readonly
```ts
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}
```
- readonly：就是将属性变成只读

### Record
```ts
type Record<K extends keyof any, T> = {
  [P in K]: T;
}
```
>- keyof any 返回 string number symbol 的联合类型
>- extends: 约束我们的类型
>- T直接返回类型

## TS进阶用法infer
>infer 是TypeScript 新增到的关键字 充当占位符
### 条件类型推断的例子
定义一个类型 如果是数组类型 就返回 数组元素的类型 否则 就传入什么类型 就返回什么类型
```ts
type Infer<T> = T extends Array<any> ? T[number] : T;
type A = Infer<(boolean | string)[]>;
type B = Infer<null>
```
**使用infer修改**
```ts
type Infer<T> = T extends Array<infer U> ? U : T;
type A = Infer<(string | Symbol)[]>
```

### 配合tuple转化union联合类型
```ts
type TupleToUni<T> = T extends Array<infer E> ? E : never;
type TTuple = [string, Symbol];
type ToUnion = TupleToUni<TTuple>; // string | Symbol
```