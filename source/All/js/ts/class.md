---
title: class 类
toc: true
date: 2021-11-03 16:00:02
---

要想面向对象，操作对象，首先便要拥有对象，那么下一个问题就是如何创建对象。要创建对象，必须要先定义类，所谓的类可以理解为对象的模型，程序中可以根据类创建指定类型的对象，举例来说：可以通过Person类来创建人的对象，通过Dog类创建狗的对象，通过Car类来创建汽车的对象，不同的类可以用来创建不同的对象。

- 定义类：

```typescript
  class 类名 {
    属性名: 类型;
    
    constructor(参数: 类型){
      this.属性名 = 参数;
    }
    
    方法名(){
      ....
    }
  
  }
```

- 示例：

```typescript
  class Person{
      name: string;
      age: number;

      constructor(name: string, age: number){
          this.name = name;
          this.age = age;
      }

      sayHello(){
          console.log(`大家好，我是${this.name}`);
      }
  }
```

- 使用类：

```typescript
  const p = new Person('孙悟空', 18);
  p.sayHello();
```
