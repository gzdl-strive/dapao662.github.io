---
title: json、修饰符
date: 2020-12-28 10:20:02
tag: ts
---

## json
```ts
let json: {a: number, b: string} = {a: 5, b: 'str'};
console.log(json.b);//str
```

## 修饰符
```ts
class Person {
    public static user:string = 'huang';//加了static（静态）不需要实例化就可以使用
    constructor(name: string, private age: number) {
        this.name = name;
    }
}
let p: Person = new Person('asdf', 18);
console.log(Person.user);
```