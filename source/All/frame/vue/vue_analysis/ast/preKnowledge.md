---
title: 前置知识
date: 2022-01-22 14:56:24
toc: true
---

## 指针思想
![ast](/assets/vueImg/vueSource/ast3.png "指针")
```js
//指针思想:pointer
// 试寻找字符串中，连续重复次数最多的字符。
let str = 'abbbcccdddddddaaabbbbcccceeeeeeeeee';

//使用双指针-滑动窗口思想：窗口收缩条件->j位置的字符不等于i位置
let i = 0, j = 1;//设置双指针

//结果集
let resultCount = 0, resultStr = '';

//循环结束条件：j遍历到最后
while (j <= str.length) {
  //窗口缩小
  if (str[i] !== str[j]) {
    if (j - i > resultCount) {
      resultCount = j - i;
      resultStr = str[i];
    }
    //i后移到j位置
    i = j;
  }
  //j是不管当str[i]和str[j]是否相等，都需要后移
  j++;
}
console.log(resultCount, resultStr);
```

## 递归深入
![ast](/assets/vueImg/vueSource/ast4.png "递归深入")
```js
//使用缓存
const cache = {};
function feb(n) {
  if (cache.hasOwnProperty(n)) {
    return cache[n];
  }
  let res = (n === 0 || n === 1) ? 1 : feb(n - 1) + feb(n - 2);
  cache[n] = res;
  return res;
}
for (let i = 0; i <= 9; i++) {
  console.log(feb(i));
}
```
![ast](/assets/vueImg/vueSource/ast5.png "递归深入")
```js
const arr = [1, 2, 3, [4, 5], [[6, [7, 8]], 9], 10];

//写法1
function convert(arr) {
  const result = [];//结果数组
  // 遍历传入的arr的每一项
  for (let i = 0; i < arr.length; i++) {
    if (typeof arr[i] === 'number') {
      // 如果遍历到的数字是number，直接放进入
      result.push({
        value: arr[i]
      })
    } else if (Array.isArray(arr[i])) {
      // 如果遍历到的这项是数组，那么就递归
      result.push({
        children: convert(arr[i])
      })
    }
  }
  return result;
}
// console.log(convert(arr));

//写法二,参数不是arr了，而是item,意味着现在item可能是数组，也可能是数字
//即，写法1的递归次数要大大小于写法2。因为写法2中，遇见什么东西都要递归一下。
function convert2(item) {
  if (typeof item === 'number') {
    return { 
      value: item
    }
  } else if (Array.isArray(item)) {
    return {
      children: item.map(_item => convert2(_item))
    }
  }
}

console.log(convert2(arr));
```

## 栈思想
```js
/*
  试编写“智能重复”smartRepeat函数，实现：
  将3[abc]变为abcabcabc
  将3[2[a]2[b]]变为aabbaabbaabb  
  将2[1[a]3[b]2[3[c]4[d]]]变为abbbcccddddcccddddabbbcccddddcccdddd

  -> [] -> 栈思想 -> 一对括号可以看作一个完整的事件
  -> 遇到[入栈，]出栈
*/

function smartRepeat(templateStr) {
  //定义一个从头开始遍历的指针
  let index = 0;
  //定义两个栈: 一个用于存储数字，一个用于存储临时字符串
  let stack1 = [], stack2 = [];
  //剩余部分
  let rest = templateStr;

  //遍历：使用while循环进行遍历,为什么不用for循环，因为for循环的步长是固定的
  while (index < templateStr.length - 1) {
    //剩余部分
    rest = templateStr.substring(index);

    //看当前剩余部分是不是以数字和[开头
    if (/^\d+\[/.test(rest)) {
      //例如：12[
      //得到这个数字,并需要把该数字入栈
      //使用match方法配合()进行捕获,捕获数字会出现在下标为1的位置
      //且需要把捕获的字符串转换成数字类型
      let times = Number(rest.match(/^(\d+)\[/)[1]);
      //把数字压入栈钟，并且把空字符串压入临时字符串栈中
      stack1.push(times);
      stack2.push('');
      //指针需要后移，移动times的长度 + 1，加1的原因是因为还需要跳过[
      index += times.toString().length + 1;
    } else if (/^\w+\]/.test(rest)) {
      //剩余部分是以字母 加 ]，那么就需要将临时字符串栈顶的那项改为当前字
      let word = rest.match(/^(\w+)\]/)[1];
      stack2[stack2.length - 1] = word;
      //指针后移，word有多少位就后移多少位
      index += word.length;
    } else if (rest[0] === ']') {
      //如果当前字符是],那么就需要进行弹栈操作
      //①先将stack1弹栈 ②stack2弹栈 ③将刚才弹出栈的单词重复指定次数拼接到到临时字符串栈顶
      let times = stack1.pop();
      let word = stack2.pop();
      //repeat是ES6的方法，比如'a'.repeat(2) => 'aa'
      stack2[stack2.length - 1] += word.repeat(times);
      index++;
    }
  }

  //循环结束后，stack1和stack2中应该都只剩下一项。返回栈2中剩下的这一项，重复栈1中剩下的这1项次数，组成的这个字符串。如果剩的个数不对，那就是用户的问题，方括号没有闭合。
  return stack2[0].repeat(stack1[0]);
}

let templateStr = '3[2[3[a]1[b]]4[d]]';
let resultStr = smartRepeat(templateStr);
console.log(resultStr)
```