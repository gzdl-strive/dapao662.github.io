---
title: 类
date: 2020-12-28 10:08:02
tag: ts
---

``` ts
class Person {
    protected name: string;
    private age: number;
    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }
    show(): void {
        console.log(`名字:${this.name},年龄:${this.age}`);
    }
}
class Staff extends Person {
    private job: string;
    constructor(name: string, age: number, job: string) {
        super(name, age);
        this.job = job;
    }
    show(): void {
        super.show();
        console.log(this.name);
        //console.log(this.age);--直接报错-->age是private
        console.log(`岗位：${this.job}`);
    }
}

let s: Staff = new Staff('666', 25, 'gnls');
s.show();
/*
名字:666,年龄:25
666
岗位：gnls
*/
```

**另一种写法**
```ts
class Person {
    constructor(private name: string, private age:number) {
        this.name = name;
        this.age = age;
    }
    public show(): void {
        console.log(`名字:${this.name},年龄:${this.age}`)
    }
}
let p: Person = new Person('666', 20);
p.show();//名字:666,年龄:20
```
