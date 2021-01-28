---
title: 类
date: 2021-01-27 15:45:20
toc: true
tag: ts
---

### 类
```ts
class Greeter {
    private greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return `hello ${this.greeting}`;
    }
}
let greeter = new Greeter('world');
```
我们声明一个 Greeter类.这个类有3个成员：一个叫做 greeting的属性，一个构造函数和一个 greet方法。你会注意到，我们在引用任何一个类成员的时候都用了 this。 它表示我们访问的是类的成员.最后一行，我们使用 new构造了 Greeter类的一个实例.它会调用之前定义的构造函数，创建一个 Greeter类型的新对象，并执行构造函数初始化它。

### 继承
```ts
class Animal {
    move(distaceInMeters: number = 0) {
        console.log(`Animal moved ${distaceInMeters}m`);
    }
}
class Dog extends Animal {
    bark() {
        console.log(`Woof! Woof!`);
    }
}
const dog = new Dog();
dog.bark();//Woof! Woof!
dog.move();//Animal moved 0m
dog.move(20);//Animal moved 20m
```
>类从基类中继承了属性和方法-这里， Dog是一个 `派生类`，它派生自 Animal `基类`，通过 `extends`关键字。派生类通常被称作 `子类`，基类通常被称作 `超类`.因为 Dog继承了 Animal的功能，因此我们可以创建一个 Dog的实例，它能够 bark()和 move()
```ts
class Animal2 {
    name: string;
    constructor(theName: string) {
        this.name = theName;
    }
    move(distaceInMeters: number = 0) {
        console.log(`${this.name} moved ${distaceInMeters}m`);
    }
}
class Snake extends Animal2 {
    constructor(name: string) {
        super(name);
    }
    move(distaceInMeters: number = 5) {
        console.log('Slitering...');
        super.move(distaceInMeters);
    }
}
class Horse extends Animal2 {
    constructor(name: string) {
        super(name);
    }
    move(distaceInMeters: number = 50) {
        console.log('Galloping...');
        super.move(distaceInMeters);
    }
}
let sam = new Snake(`Sammy ths Python`);
let tom: Animal2 = new Horse('Tommy the Palomino');

sam.move();
//Slitering...
//Sammy ths Python moved 5m
tom.move(60);
//Galloping...
//Tommy the Palomino moved 60m
```
>派生类包含了一个构造函数，它 必须调用 `super()`，它会执行基类的构造函数,**而且，在构造函数里访问 this的属性之前，我们 一定要调用 super()。 这个是TypeScript强制执行的一条重要规则**.`Snake类`和 `Horse类`都创建了 move方法，它们重写了从 Animal继承来的 move方法，使得 move方法根据不同的类而具有不同的功能.注意，即使 tom被声明为 Animal类型，但因为它的值是 Horse，调用 tom.move(60)时，它会调用 Horse里重写的方法


### 公共，私有与受保护的修饰符
#### 默认为public
>你也可以明确的将一个成员标记成 `public`
```ts
class Animal3 {
    public name: string;
    public constructor(theName: string) {
        this.name = theName;
    }
    public move(distaceInMeters: number = 0) {
        console.log(`${this.name} moved ${distaceInMeters}m`)
    }
}
```
#### 理解private
>当成员被标记成`private`时，它就不能在声明它的类的外部访问
```ts
class Animal4 {
    private name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}
// new Animal4('cat').name;error! 属性“name”为私有属性，只能在类“Animal4”中访问。
```
**TypeScript使用的是结构性类型系统,当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。**
**然而，当我们比较带有 private或 protected成员的类型的时候，情况就不同了.如果其中一个类型里包含一个 private成员，那么只有当另外一个类型中也存在这样一个 private成员， 并且它们都是来自同一处声明时，我们才认为这两个类型是兼容的。 对于 protected成员也使用这个规则。**
*以下例子说明：*
```ts
class Animal5 {
    private name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}
class Rhino extends Animal5 {
    constructor() {
        super('Rhino');
    }
}
class Employee {
    private name: string;
    constructor(theName: string) {
        this.name = theName;
    }
}
let animal5 = new Animal5('Goat');
let rhino = new Rhino();
let employee = new Employee('Bob');

animal5 = rhino;
// animal5 = employee;
//不能将类型“Employee”分配给类型“Animal5”。类型具有私有属性“name”的单独声明
```
这个例子中有 Animal和 Rhino两个类， Rhino是 Animal类的子类。 还有一个 Employee类，其类型看上去与 Animal是相同的。因为 Animal和 Rhino共享了来自 Animal里的私有成员定义 private name: string，因此它们是兼容的。然而 Employee却不是这样。当把 Employee赋值给 Animal的时候，得到一个错误，说它们的类型不兼容。 尽管 Employee里也有一个私有成员 name，但它明显不是 Animal里面定义的那个

#### 理解protected
>`protected`修饰符与 private修饰符的行为很相似，但有一点不同， protected成员在派生类中仍然可以访问
```ts
class Person {
    protected name: string;
    constructor(name: string) {
        this.name = name;
    }
}
class Employee2 extends Person {
    private department: string;
    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }
    public getElevatorpitch() {
        return `Hello, my name is ${this.name}, and I work in ${this.department}`
    }
}
let howard = new Employee2('Howard', 'Sales');
console.log(howard.getElevatorpitch());//Hello, my name is Howard, and I work in Sales
// console.log(howard.name);属性“name”受保护，只能在类“Person”及其子类中访问
```
**注意，我们不能在 Person类外使用 name，但是我们仍然可以通过 Employee类的实例方法访问，因为 Employee是由 Person派生而来的.构造函数也可以被标记成 protected。 这意味着这个类不能在包含它的类外被实例化，但是能被继承**
```ts
class Person2 {
    protected name: string;
    protected constructor(theName: string) {
        this.name = theName;
    }
}
class Employee3 extends Person {
    private department: string;
    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }
    public getElevatorpitch() {
        return `Hello, my name is ${this.name}, and I work in ${this.department}`
    }
}
let howard2 = new Employee3('Howard2', 'Sales2');
// let john = new Person2('John');类“Person2”的构造函数是受保护的，仅可在类声明中访问
```

### readonly修饰符
>你可以使用 readonly关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。
```ts
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor(theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus('Man with the 8 strong legs');
// dad.name = 'Man width the 3-piece suit';无法分配到 "name" ，因为它是只读属性
```
### 参数属性
在上面的例子中，我们必须在Octopus类里定义一个只读成员 name和一个参数为 theName的构造函数，并且立刻将 theName的值赋给 name，这种情况经常会遇到。 参数属性可以方便地让我们在一个地方定义并初始化一个成员。
*下面是Octopus的改版-》使用了参数属性*
```ts
class Octopus2 {
    readonly numberOfLegs: number = 8;
    constructor(readonly name: string) {
        //...
    }
}
```
**注意看我们是如何舍弃了 theName，仅在构造函数里使用 readonly name: string参数来创建和初始化 name成员.我们把声明和赋值合并至一处。**
>参数属性通过给构造函数参数前面添加一个访问限定符来声明。使用 private限定一个参数属性会声明并初始化一个私有成员；对于 public和 protected来说也是一样。


### 存取器
>TypeScript支持通过`getters/setters`来截取对对象成员的访问。 它能帮助你有效的控制对对象成员的访问.
*下面来看如何把一个简单的类改写成使用 get和 set。 首先，我们从一个没有使用存取器的例子开始。*
```ts
class Employee4 {
    fullName: string;
}
let employee4 = new Employee4();
employee4.fullName = 'Bob Smith';
if (employee4.fullName) {
    console.log(employee4.fullName);//Bob Smith
}
```
我们可以随意的设置 fullName，这是非常方便的，但是这也可能会带来麻烦
*下面这个版本里，我们先检查用户密码是否正确，然后再允许其修改员工信息。 我们把对 fullName的直接访问改成了可以检查密码的 set方法。 我们也加了一个 get方法，让上面的例子仍然可以工作。*
```ts
let passcode = 'secret passcode';
class Employee5 {
    private _fullName: string;
    get fullName(): string {
        return this._fullName;
    }
    set fullName(newName: string) {
        if (passcode && passcode === 'secret passcode') {
            this._fullName = newName;
        } else {
            console.log(`Error: UnAuthorized update fo Employee5`);
        }
    }
}
let employee5 = new Employee5();
employee5.fullName = 'Bob Smith2';
if (employee5.fullName) {
    //Accessors are only available when targeting ECMAScript 5 and higher.//需要指定编译到版本ES5或以上
    //执行tsc -t es5 xxx.ts
    console.log(employee5.fullName);//Bob Smith2
}
```
>首先，存取器要求你将编译器设置为输出ECMAScript 5或更高。 不支持降级到ECMAScript 3。其次，只带有 get不带有 set的存取器自动被推断为 readonly.这在从代码生成 .d.ts文件时是有帮助的，因为利用这个属性的用户会看到不允许够改变它的值。


### 静态属性
>到目前为止，我们只讨论了类的实例成员，那些仅当类被实例化的时候才会被初始化的属性.我们也可以创建类的静态成员，这些属性存在于类本身上面而不是类的实例上。
**在这个例子里，我们使用 static定义 origin，因为它是所有网格都会用到的属性.每个实例想要访问这个属性的时候，都要在 origin前面加上类名.如同在实例属性上使用 this.前缀来访问属性一样，这里我们使用 Grid.来访问静态属性。**
```ts
class Grid {
    static origin = { x: 0, y: 0 };
    calculateDistanceFromOrigin(point: { x: number; y: number; }) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    //scale使用了参数属性
    constructor(private scale: number) {

    }
}
let grid1 = new Grid(1.0);
let grid2 = new Grid(5.0);

console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }));//14.142135623730951
console.log(grid2.calculateDistanceFromOrigin({ x: 10, y: 10 }));//2.8284271247461903

```

### 抽象类
>抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化.不同于接口，抽象类可以包含成员的实现细节
**abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法。**
```ts
abstract class Animal6 {
    abstract makeSound(): void;
    move(): void {
        console.log('romaing this earch...');
    }
}
```
**抽象类中的抽象方法不包含具体实现并且必须在派生类中实现**
抽象方法的语法与接口方法相似.两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 abstract关键字并且可以包含访问修饰符
```ts
abstract class Department {
    //参数属性
    constructor(public name: string) {

    }
    printName(): void {
        console.log('Department name: ' + this.name);
    }
    abstract printMeeting(): void;//必须在派生类中实现
}
class AccountingDepartment extends Department {
    constructor() {
        super('Accounting and Auditing');//在派生类的构造函数中必须调用 super()
    }
    printMeeting(): void {
        console.log(`The Accounting Deparment meets each Monday at 10am`);
    }
    generateReports(): void {
        console.log(`Generating accounting reports...`);
    }
}
let department: Department;// 允许创建一个对抽象类型的引用
// department = new Department();无法创建抽象类的实例
department = new AccountingDepartment();//允许对一个抽象子类进行实例化和赋值
department.printName();//Department name: Accounting and Auditing
department.printMeeting();//The Accounting Deparment meets each Monday at 10am
// department.generateReports();//类型“Department”上不存在属性“generateReports”
```

### 高级技巧
#### 构造函数
>当你在TypeScript里声明了一个类的时候，实际上同时声明了很多东西。 首先就是类的 实例的类型。
```ts
class Greeter2 {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return 'hello' + this.greeting;
    }
}
let greeter2: Greeter2;
greeter2 = new Greeter2('world');
console.log(greeter2.greet());//helloworld
```
**这里，我们写了 let greeter: Greeter，意思是 Greeter类的实例的类型是 Greeter**.我们也创建了一个叫做 构造函数的值,这个函数会在我们使用 new创建类实例的时候被调用
*下面我们来看看，上面的代码被编译成JavaScript后是什么样子的：*
Greeter2重复定义，使用Greeter3代替
```ts
let Greeter3 = (function () {
    function Greeter3(message) {
        this.greeting = message;
    }
    Greeter3.prototype.greet = function () {
        return 'hello, ' + this.greeting;
    }
    return Greeter3;
}());
var greeter3;
greeter3 = new Greeter3('world');
console.log(greeter3.greet());
```
**上面的代码里， let Greeter将被赋值为构造函数.当我们调用 new并执行了这个函数后，便会得到一个类的实例。这个构造函数也包含了类的所有静态属性。换个角度说，我们可以认为类具有 实例部分与 静态部分这两个部分**
```ts
class Greeter4 {
    static standardGreeting = 'Greeter4:hello, there';
    greeting: string;
    greet() {
        if (this.greeting) {
            return 'Greeter4:Hello, ' + this.greeting;
        } else {
            return Greeter4.standardGreeting;
        }
    }
}
//?greeter1与之前看到的一样。 我们实例化 Greeter4类，并使用这个对象
let greeter1: Greeter4;
greeter1 = new Greeter4();
console.log(greeter1.greet());//Greeter4:hello, there
```

**再之后，我们直接使用类。 我们创建了一个叫做 greeterMaker的变量,这个变量保存了这个类或者说保存了类构造函数,然后我们使用 typeof Greeter4，意思是取Greeter4类的类型，而不是实例的类型,或者更确切的说，"告诉我 Greeter4标识符的类型"，也就是构造函数的类型**
这个类型包含了类的所有静态成员和构造函数.之后，就和前面一样，我们在 greeterMaker上使用 new，创建 Greeter的实例
```ts
let greeterMaker: typeof Greeter4 = Greeter4;
greeterMaker.standardGreeting = 'Greeter4:hey here';

let greeter12: Greeter4 = new greeterMaker();
console.log('greeterMaker:',greeter12.greet());//greeterMaker: Greeter4:hey here
```


#### 把类当成接口使用
>如上一节里所讲的，类定义会创建两个东西：类的实例类型和一个构造函数.因为类可以创建出类型，所以你能够在允许使用接口的地方使用类。
```ts
class Point {
    x: number;
    y: number;
}
interface Point3d extends Point {
    z: number;
}
let point3d: Point3d = { x: 1, y: 2, z: 3 };
```



