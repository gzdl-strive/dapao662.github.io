---
title: ES6-Set、Map、Class类
date: 2021-11-16 09:46:02
toc: true
---

## Set

ES6 提供了新的数据结构 Set（集合）。它类似于数组，但成员的值都是唯一的，集合实现了 iterator 接口，所以可以使用『扩展运算符』和『for…of…』进行遍历，集合的属性和方法：
(1) size 返回集合的元素个数
(2) add 增加一个新元素，返回当前集合
(3) delete 删除元素，返回 boolean 值
(4) has 检测集合中是否包含某个元素，返回 boolean 值
(5) clear 清空集合，返回 undefined

```js
//声明一个 set
let s = new Set();
let s2 = new Set(["大事儿", "小事儿", "好事儿", "坏事儿", "小事儿"]);

// 元素个数
console.log(s2.size);
// 添加新的元素
s2.add("喜事儿");
// 删除元素
s2.delete("坏事儿");
// 检测
console.log(s2.has("糟心事"));
// 清空
s2.clear();
console.log(s2);

for (let v of s2) {
  console.log(v);
}
```

**Set 实践**

```js
let arr = [1, 2, 3, 4, 5, 4, 3, 2, 1];
//1、数组去重
let result = [...new Set(arr)];
//[1, 2, 3, 4, 5]

//2、交集
let arr2 = [4, 5, 6, 5, 4];
let result = [...new Set(arr)].filter((item) => {
  let s2 = new Set(arr2);
  if (s2.has(item)) {
    return true;
  } else {
    return false;
  }
});

//3、并集
let union = [...new Set([...arr, ...arr2])];

//4、差集
let diff = [...new Set(arr)].filter((item) => !new Set(arr2).has(item));
```

## Map

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合。但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。Map 也实现了 iterator 接口，所以可以使用『扩展运算符』和『for…of…』进行遍历。Map 的属性和方法：
(1) size 返回 Map 的元素个数
(2) set 增加一个新元素，返回当前 Map
(3) get 返回键名对象的键值
(4) has 检测 Map 中是否包含某个元素，返回 boolean 值
(5) clear 清空集合，返回 undefined

```js
//声明 Map
let m = new Map();

//添加元素
m.set("name", "尚硅谷");
m.set("change", function () {
  console.log("我们可以改变你!!");
});
let key = {
  school: "ATGUIGU",
};
m.set(key, ["北京", "上海", "深圳"]);

//size
console.log(m.size);

//删除
m.delete("name");

//获取
console.log(m.get("change"));
console.log(m.get(key));

//清空
m.clear();

//遍历
for (let v of m) {
  console.log(v);
}
```

## class 类

ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过 class 关键字，可以定义类。基本上，ES6 的 class 可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的 class 写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。
知识点：
(1) class 声明类
(2) constructor 定义构造函数初始化
(3) extends 继承父类
(4) super 调用父级构造方法
(5) static 定义静态方法和属性
(6) 父类方法可以重写

```js
//父类
class Phone {
  //构造方法
  constructor(brand, color, price) {
    this.brand = brand;
    this.color = color;
    this.price = price;
  }
  //对象方法
  call() {
    console.log("我可以打电话!!!");
  }
}
//子类
class SmartPhone extends Phone {
  constructor(brand, color, price, screen, pixel) {
    super(brand, color, price);
    this.screen = screen;
    this.pixel = pixel;
  }
  //子类方法
  photo() {
    console.log("我可以拍照!!");
  }
  playGame() {
    console.log("我可以玩游戏!!");
  }
  //方法重写
  call() {
    console.log("我可以进行视频通话!!");
  }
  //静态方法
  static run() {
    console.log("我可以运行程序");
  }
  static connect() {
    console.log("我可以建立连接");
  }
}
//实例化对象
const Nokia = new Phone("诺基亚", "灰色", 230);
const iPhone6s = new SmartPhone("苹果", "白色", 6088, "4.7inch", "500w");
//调用子类方法
iPhone6s.playGame();
//调用重写方法
iPhone6s.call();
//调用静态方法
SmartPhone.run();
```
