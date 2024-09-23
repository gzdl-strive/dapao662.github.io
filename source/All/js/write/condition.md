---
title: 使用js编写更好的条件语句
date: 2021-09-04 16:13:24
toc: true
---

>1. 数组方法 Array.includes
>2. 提前退出/提前返回
>3. 对象字面量 代替switch语句
>4. 默认参数结构
>5. 用上Array.some Array.every

### 1、数组方法Array.includes
```js
function printAnimals(animal) {
  // if (animal === 'cat' || animal === 'dog') {
  //   console.log(`I have a ${animal}`);
  // }

  //使用includes
  let animals = ['dog', 'cat'];
  if (animals.includes(animal)) {
    console.log(`I have a ${animal}`);
  }
}
```

### 2、提前退出/提前返回
```js
//使用默认参数和解构赋值
const printAnimalsDetails = ({ type, gender, name } = {}) => {
  if (!type) return 'no animal type';
  if (!gender) return 'no animal gender';
  if (!name) return 'no animal name';
  return `${name} is a ${gender} ${type}`;
}
```

### 3、对象字面量代替switch
>需求：基于颜色打印水果
```js
function printFruits(color) {
  switch(color) {
    case 'yellow':
      return '香蕉';
    case 'red':
      return '苹果';
    default:
      return '';
  }
}
```
**使用对象字面量**
```js
const fruitColor = {
  red: '苹果',
  yellow: '香蕉';
}
function printFruits(color) {
  return fruitColor[color] || '';
}
```



