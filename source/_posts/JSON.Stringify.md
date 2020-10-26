---
title: JSON. Stringify
date: 2020-10-26 20:36:03
tags: js
---

> 序列化选项： `JSON.stringify()` ：**把一个js对象转换为JSON字符串**

要接受的参数除了js对象外，还可以接收两个参数：

* 第一个参数是过滤器，可以是一个数组，也可以是一个函数
* 第二个参数是一个选项，便是是否在JSON字符串中保留缩进

### 过滤器

``` js
const book = {
    "title": "web前端基础",
    "authors": ["csgo", "rush", "B"],
    total: 999
    year: 2020
}
```

**参数为一个数组时**

``` js
const jsonText = JSON.stringify(book, ["title", "year"]);
console.log(jsonText);
```

输出结果为: `{"title":"web前端基础","year":2020}`

**参数为函数时**
传入的函数接收两个参数，属性（键）名和属性值。根据属性（键）名可以知道应该如何处理要序列化的对象中的属性，属性名只能是字符串，而在值并非键值对儿结构的值时，键名可以是空字符串为了改变序列化对象的结果，函数返回的值就是相应键的值。不过要注意，如果函数返回了undefined，那么相应的属性就会被忽略。

```js
const jsonText2 = JSON.stringify(book, function(key, value) {
    switch (key) {
        case "authors":
            return value.json("-");
        case "total":
            return undefined;
        case "year":
            return `${value}年出版`
        default:
            return value;
    }
});
console.log(jsonText2);
```
输出结果为：`{"title":"web前端基础","authors":"csgo-rush-b","year":"2020年出版"}`

### 字符串缩进

**如果这个参数是一个数值，那它表示的是每个级别缩进的空格数。最大缩进空格为10，大于10会自动转换为10**

```js
const jsonText3 = JSON.stringify(book, null, 4);
console.log(jsonText3);
```
输出结果为：
```js
{
    "title": "web前端基础",
    "authors": [
        "csgo",
        "rush",
        "b"
    ],
    "total": 999,
    "year": 2020
}
```

**如果缩进参数是一个字符串而非数值，则这个字符串将在JSON字符串中被用作缩进字符（不再使用空格）超过10个，自动转换为10**
```js
const jsonText4 = JSON.stringify(book, null, "---");
console.log(jsonText4);
```
输出结果为：
```js
{
---"title": "web前端基础",
---"authors": [
------"csgo",
------"rush",
------"b"
---],
---"total": 999,
---"year": 2020
}
```

------------------
### JSON.toJSON()
>有时候`JSON.stringify()`还是不能满足对某些对象进行自定义序列化的需求。可以使用`toJSON()`,返回其自身的JSON数据格式。
```js
const book2 = {
    "title": "js高级程序设计（第三版）",
    "authors": ["csgo", "rush", "b"],
    total: 999,
    year: 2020,
    toJSON: function () {
        return this.title
    }
};
const jsonText5 = JSON.stringify(book2);
console.log(jsonText5);
```
输出结果为：
```js
"js高级程序设计（第三版）"
```