---
title: ts-基础类型
date: 2021-01-22 15:39:02
tag: ts
---

## 布尔值
```ts
let isDone: boolen = false;
```

## 数字
```ts
// 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。
let num1: number = 10;
let num2: number = 0xf00;
```

## 字符串
```ts
let name1: string = 'hello';
```

## 数组
```ts
// 方法一、
let list: (number | string)[] = [1, 2, 3, 'hello'];
// 方法二、数组泛型Array<元素类型>
let list2: Array<number | string> = [5, 6, 'world'];
```

## 元组(Tuple)
```ts
// 元组Tuple:元组类型允许表示一个已知元素数量和类型的数组，各元素的类型不必相同。 比如，你可以定义一对值分别为 string和number类型的元组
let x: [string, number];
x = ['hello', 10];
//当访问一个已知索引的元素，会得到正确的类型
console.log(x[0].substr(1));
// console.log(x[1].substr(1));//Error, 'number' does not have 'substr'
// 当访问一个越界的元素，会使用联合类型替代
```

## 枚举
```ts
enum Color { Red, Green, Blue };
// 默认情况下，从0开始为元素编号
let c: Color = Color.Green;
console.log(c);//1
//你也可以手动的指定成员的数值
enum Color1 { Red = 1, Green, Blue };
let c1: Color1 = Color1.Green;
console.log(c1);//2
//或者，全部都采用手动赋值
enum Color2 { Red = 1, Green = 5, Blue = 2};
let c2: Color2 = Color2.Blue;
console.log(c2);//2
//枚举类型提供的一个便利是你可以由枚举的值得到它的名字。 例如，我们知道数值为2，但是不确定它映射到Color里的哪个名字，我们可以查找相应的名字
enum Color3 { Red = 10, Green, Blue };
let colorName: string = Color3[11];
console.log(colorName);//Green
```

## Any
>有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。那么我们可以使用 any类型来标记这些变量.
```ts
let notSure: any = 4;
notSure = 'may be I want check it';
notSure = false;
console.log(notSure);//false
```
**在对现有代码进行改写的时候，`any`类型是十分有用的，它允许你在编译时可选择地包含或移除类型检查。**
**你可能认为` Object`有相似的作用，就像它在其它语言中那样。 但是` Object`类型的变量只是允许你给它赋任意值 - 但是却不能够在它上面调用任意的方法，即便它真的有这些方法：**
```ts
let prettySure: Object = 4;
// prettySure.toFixed();
```
**当你只知道一部分数据的类型时，any类型也是有用的。 比如，你有一个数组，它包含了不同的类型的数据**
```ts
let list3: any[] = [1, 'str', false];
list3[0] = 'hello';
console.log(list3);
```

## void
>某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 `void`
```ts
function sayHello(name: string): void {
    console.log(`hello ${name}`);
}
sayHello('Jack');
```
**声明一个void类型的变量没有什么大用，因为你只能为它赋予undefined和null**
```ts
let unusable: void = undefined || null;
```

## Null和Undefined
>TypeScript里，undefined和null两者各自有自己的类型分别叫做undefined和null。 和 void相似，它们的本身的类型用处不是很大
```ts
let u: undefined = undefined;
let n: null = null;
// ? 默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量
// * 然而，当你指定了--strictNullChecks标记，null和undefined只能赋值给void和它们各自
// ? 这能避免 很多常见的问题。 也许在某处你想传入一个 string或null或undefined，你可以使用联合类型string | null | undefined。
// * 注意：我们鼓励尽可能地使用--strictNullChecks
```

## never:never类型表示的是那些永不存在的值的类型
```ts
//  例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。
// ? never类型是任何类型的子类型，也可以赋值给任何类型
// ? 然而，没有类型是never的子类型或可以赋值给never类型（除了never本身之外）。 即使 any也不可以赋值给never。
//下面是一些返回never类型的函数:
// ? 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
// error('bad');
// ? 推断的返回值类型为never
function fail() {
    return error('something failed');
}
// fail();
```

## Object
>Object: object表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型
```ts
//?使用object类型，就可以更好的表示像Object.create这样的API。
declare function create(o: object | null): void;
// create({ props: 0 });//ok
// create(null);//ok
// create(42); // Error
// create("string"); // Error
// create(false); // Error
// create(undefined); // Error
```

## 类型断言
>有时候你会遇到这样的情况，你会比TypeScript更了解某个值的详细信息。 通常这会发生在你清楚地知道一个实体具有比它现有类型更确切的类型。
>通过类型断言这种方式可以告诉编译器，“相信我，我知道自己在干什么”。类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。它没有运行时的影响，只是在编译阶段起作用。TypeScript会假设你，程序员，已经进行了必须的检查
**类型断言有两种形式**
###  其一是“尖括号”语法
```ts
let someValue: any = 'hello world';
let strLength: number = (<string>someValue).length;
console.log(strLength);
```
### as语法
```ts
let someValue2: any = 'hello world2';
let strLength2: number = (someValue2 as string).length;
console.log(strLength2);
```
>两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在TypeScript里使用JSX时，只有 as语法断言是被允许的。