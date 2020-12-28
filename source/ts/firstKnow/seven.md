---
title: 存取器
date: 2020-12-28 10:24:02
tag: ts
---

```ts
class Person {
    private _name: string;
    constructor(name: string) {
        this._name = name;
    }
    get name():string {
        return this._name;
    }
    set name(newName:string) {
        if (newName.length < 8) {
            throw new Error('名字最少8位');
        } else {
            this._name = newName;
        }
    }
    show(): void {
        console.log(this._name);
    }
}
let p:Person=new Person('huang');
p.show();//huang
p.name="huangjmlsadjf";
console.log(p.name);//huangjmlsadjf
```
**运行报错**
```ts
Accessors are only available when targeting ECMAScript 5 and higher.//需要指定编译到版本ES5或以上
// 执行tsc -t es5 xxx.ts
// node xxx.js
```