---
title: 抽象、接口
date: 2020-12-28 10:33:02
tag: ts
---

## 抽象
>抽象类只能作为父级使用，不能之间实例化

```ts
abstract class Shape {
    abstract draw(gd): void;
    abstract area(): number;
    abstract pointin(x: number, y: number):boolean;
}
class Rect extends Shape {
    constructor(private width: number, private height: number) {
        super();
    }
    draw(gd): void {
        gd.fillRect(0, 0, this.width, this.height);
    }
    area(): number {
        return this.width*this.height;
    }
    pointin(x: number, y: number): boolean {
        return false;
    }
}

class Circle extends Shape {
    constructor(private cx: number, private cy: number, private r: number) {
        super();
    }
    draw(gd): void {
        gd.arc();
    }
    area(): number {
        return Math.PI * Math.pow(this.r, 2);
    }
    pointin(x: number, y: number): boolean {
        return false;
    }
}
let a: Rect;
a = new Rect(400, 300);
console.log(a.area());//120000

let c: Circle;
c = new Circle(400, 300, 150);
console.log(c.area());//70685.83470577035
```

## 接口

```ts
interface Shape {
    area(): number;
}
class Rect implements Shape {
    constructor(private width:number,private height:number) {
    }
    area(): number {
        return this.width*this.height;
    }
}
let r: Shape = new Rect(100, 200);
console.log(r.area());//20000
```