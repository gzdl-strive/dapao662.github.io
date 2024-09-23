---
title: 单元格合并-行合并
date: 2021-07-08 09:21:24
toc: true
---

>Table组件的`column`属性中，`render`渲染的返回值如下
```js
column: [
  ...,
  render: (text, row, index) => {
    let obj = {
      children: text,
      props: {
      }
    }
    if (index === 0 || (index > 0 && text !== data[index - 1].groupName)) {
      obj.props.rowSpan = getMergeSpan('groupName', row, index, data);
    } else {
      //当前行和上一行的值相等
      obj.props.rowSpan = 0;//行合并个数
    }
    return obj;
  }
]        
```

**getMergeSpan方法**
```js
//接收四个参数分别为：列名称(例如groupName)、行数据、当前索引、数据数组
//该方法用于判断当前行的合并个数
const getMergeSpan = (colName, row, index, data) => {
  let temp = row[colName];//存储当前行的值
  let count = 0;//合并个数初始化
  for (let i = index; i < data.length; i++) {//从当前索引开始遍历，计算合并单元格的个数
    if (temp !== data[i][colName]) {
      break;
    } else {
      count += 1;
    }
  }
  return count;
}
```

