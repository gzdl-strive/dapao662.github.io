---
title: 函数
date: 2021-01-28 09:59:20
toc: true
tag: ts
---

### 函数类型
```ts
function add(x: number, y: number): number {
    return x + y;
}
let myAdd = function (x: number, y: number): number { return x + y };
```
**我们可以给每个参数添加类型之后再为函数本身添加返回值类型。 TypeScript能够根据返回语句自动推断出返回值类型，因此我们通常省略它。**

### 书写完整函数类型
```ts
let myAdd2: (x: number, y: number) => number = function(x: number, y: number): number {
    return x + y
}
```
**函数类型包含两部分：参数类型和返回值类型。 当写出完整函数类型的时候，这两部分都是需要的。**
我们以参数列表的形式写出参数类型，为每个参数指定一个名字和类型。
*我们也可以这么写：*
```ts
let myAdd3: (baseValue: number, increment: number) => number
= function(x: number, y: number): number { return x + y; }
```
只要参数类型是匹配的，那么就认为它是有效的函数类型，而不在乎参数名是否正确.第二部分是返回值类型。 对于返回值，我们在函数和返回值类型之前使用( =>)符号，使之清晰明了.如之前提到的，返回值类型是函数类型的必要部分，如果函数没有返回任何值，你也必须指定返回值类型为 void而不能留空.函数中使用的捕获变量不会体现在类型里。 实际上，这些变量是函数的隐藏状态并不是组成API的一部分。

### 推断类型
>尝试这个例子的时候，你会发现如果你在赋值语句的一边指定了类型但是另一边没有类型的话，TypeScript编译器会自动识别出类型：
```ts
//myAdd has the full function type
let myAdd4 = function(x: number, y: number): number { return x + y }
//The paramseters `x` and `y` have the type number
let myAdd5: (baseValue: number, increment: number) => number 
= function(x, y) { return x + y };
```
**这叫做“按上下文归类”，是类型推论的一种。 它帮助我们更好地为程序指定类型**

### 可选参数和默认参数
>TypeScript里的每个函数参数都是必须的。 这不是指不能传递 null或undefined作为参数，而是说编译器检查用户是否为每个参数都传入了值。
**编译器还会假设只有这些参数会被传递进函数,简短地说，传递给一个函数的参数个数必须与函数期望的参数个数一致。**
```ts
function buildName(firstName: string, lastName: string) {
    return firstName + ' ' + lastName;
}
// let result1 = buildName('bob');//应有 2 个参数，但获得 1 个,未提供 "lastName" 的自变量。
// let result2 = buildName('bob', 'adams', 'sr')//应有 2 个参数，但获得 3 个
let result3 = buildName('bob', 'adams');
```
JavaScript里，每个参数都是可选的，可传可不传。 没传参的时候，它的值就是`undefined`。
在TypeScript里我们可以在参数名旁使用 `?`实现可选参数的功能
*比如，我们想让`last name`是可选的：*
```ts
function buildName2(firstName: string, lastName ?: string) {//必选参数不能位于可选参数后
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
let result21 = buildName2('bob');
// let result22 = buildName2('bob', 'adams', 'str')error
let result23 = buildName('bob', 'adams');
```
**可选参数必须跟在必须参数后面。** 如果上例我们想让first name是可选的，那么就必须调整它们的位置，把first name放在后面。
**在TypeScript里，我们也可以为参数提供一个默认值当用户没有传递这个参数或传递的值是undefined时**
*它们叫做有默认初始化值的参数*
```ts
function buildName3(firstName: string, lastName = 'smith') {
    return firstName + ' ' + lastName;
}
let result31 = buildName3('bob');
let result32 = buildName3('bob', 'admas');
let result33 = buildName3('bob', undefined);
```
**在所有必须参数后面的带默认初始化的参数都是可选的，与可选参数一样，在调用函数的时候可以省略**
**也就是说可选参数与末尾的默认参数共享参数类型。**
```ts
function buildName01(firstName: string, lastName?: string) {
    // ...
}
// 和
function buildName02(firstName: string, lastName = "Smith") {
    // ...
}
```
*共享同样的类型`(firstName: string, lastName?: string) => string`*
**默认参数的默认值消失了，只保留了它是一个可选参数的信息,与普通可选参数不同的是，带默认值的参数不需要放在必须参数的后面,如果带默认值的参数出现在必须参数前面，用户必须明确的传入 undefined值来获得默认值**
```ts
function buildName4(firstName = 'will', lastName: string) {
    return firstName + ' ' + lastName;
}
// let result41 = buildName4('bob');未提供 "lastName" 的自变量。(只提供一个参数，会认为是fistName的)
let result42 = buildName4(undefined, 'adasm');
```

### 剩余参数
>必要参数，默认参数和可选参数有个共同点：它们表示某一个参数
**有时，你想同时操作多个参数，或者你并不知道会有多少参数传递进来。 在JavaScript里，你可以使用 arguments来访问所有传入的参数**
在TypeScript里，你可以把所有参数收集到一个变量里：
```ts
function buildName5(firstName: string, ...restOfName: string[]) {
    return firstName + ' ' + restOfName.join(' ');
}
let employeeName = buildName5('Joseph', 'Samuel', 'Lucas', 'Mackinzie');
console.log('剩余参数:', employeeName);//剩余参数: Joseph Samuel Lucas Mackinzie
```
**剩余参数会被当做个数不限的可选参数。 可以一个都没有，同样也可以有任意个**
编译器创建参数数组，名字是你在省略号（ ...）后面给定的名字，你可以在函数体内使用这个数组
这个省略号也会在带有剩余参数的函数类型定义上使用到：
```ts
function buildName6(firstName: string, ...restOfName: string[]) {
    return firstName + ' ' + restOfName.join(' ');
}
let buildNameFunc: (fN: string, ...rest: string[]) => string = buildName6;
```

### this和箭头函数
JavaScript里,`this`的值在函数被调用的时候才会指定
这是个既强大又灵活的特点，但是你需要花点时间弄清楚函数调用的上下文是什么。但众所周知，这不是一件很简单的事，尤其是在返回一个函数或将函数当做参数传递的时候。
```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: " + pickedCard.card + " of " + pickedCard.suit);
```
可以看到`createCardPicker`是个函数，并且它又返回了一个函数,会发现它报错了.**因为 `createCardPicker`返回的函数里的this被设置成了`window`而不是deck对象.**因为我们只是独立的调用了 cardPicker()。 顶级的非方法式调用会将 this视为window。 （注意：在严格模式下， this为undefined而不是window）
**为了解决这个问题，我们可以在函数被返回时就绑好正确的this**.这样的话，无论之后怎么使用它，都会引用绑定的‘deck’对象。 我们需要改变函数表达式来使用ECMAScript 6箭头语法。 **箭头函数能保存函数创建时的 this值，而不是调用时的值：**
```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: " + pickedCard.card + " of " + pickedCard.suit);//card: 1 of spades
```
更好事情是，TypeScript会警告你犯了一个错误.如果你给编译器设置了--noImplicitThis标记。 它会指出 this.suits[pickedSuit]里的this的类型为any。

### this参数
**不幸的是，this.suits[pickedSuit]的类型依旧为any。 这是因为 this来自对象字面量里的函数表达式。**修改的方法是，提供一个显式的 this参数。 this参数是个假的参数，它出现在参数列表的最前面：
```ts
function f(this: void) {
    //make sure `this` is unusable in this standalone function
}
```
让我们往例子里添加一些接口，Card 和 Deck，让类型重用能够变得清晰简单些：
```ts
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck2: Deck = {
    suits: ['hearts', 'spades', 'clubs', 'diamonds'],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}
let cardPicker2 = deck2.createCardPicker();
let pickedCard2 = cardPicker2();
console.log("card2: " + pickedCard2.card + " of " + pickedCard2.suit);//card2: 1 of spades
```
现在TypeScript知道createCardPicker期望在某个Deck对象上调用.也就是说 this是Deck类型的，而非any，因此--noImplicitThis不会报错了

### this参数在回调函数里
你可以也看到过在回调函数里的this报错，当你将一个函数传递到某个库函数里稍后会被调用时。因为当回调被调用的时候，它们会被当成一个普通函数调用， this将为undefined.稍做改动，你就可以通过 this参数来避免错误。
首先，库函数的作者要指定 this的类型：
...

### 重载
**JavaScript本身是个动态语言。 JavaScript里函数根据传入不同的参数而返回不同类型的数据是很常见的。**
```ts
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
console.log("card3: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard3 = pickCard(15);
console.log("card3: " + pickedCard3.card + " of " + pickedCard2.suit);
// card3: 2 of diamonds
// card3: 2 of hearts
```
pickCard方法根据传入参数的不同会返回两种不同的类型。 如果传入的是代表纸牌的对象，函数作用是从中抓一张牌。 如果用户想抓牌，我们告诉他抓到了什么牌。 但是这怎么在类型系统里表示呢。
方法是为同一个函数提供多个函数类型定义来进行函数重载。 编译器会根据这个列表去处理函数的调用。 下面我们来重载 pickCard函数。
```ts
let suits2 = ["hearts", "spades", "clubs", "diamonds"];

function pickCard2(x: {suit: string; card: number; }[]): number;
function pickCard2(x: number): {suit: string; card: number; };
function pickCard2(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck2 = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard4 = myDeck2[pickCard2(myDeck2)];
console.log("card: " + pickedCard4.card + " of " + pickedCard4.suit);

let pickedCard5 = pickCard2(15);
console.log("card: " + pickedCard5.card + " of " + pickedCard5.suit);
/*
card: 2 of diamonds
card: 2 of spades
*/
```

