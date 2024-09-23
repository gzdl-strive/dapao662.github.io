---
title: 扁平数据结构转Tree
date: 2021-07-30 15:43:24
toc: true
---

```js
let arr = [
    {id: 1, name: '部门1', pid: 0},
    {id: 2, name: '部门2', pid: 1},
    {id: 3, name: '部门3', pid: 1},
    {id: 4, name: '部门4', pid: 3},
    {id: 5, name: '部门5', pid: 4},
]
//转换成树形结构如下：
[
    {
        "id": 1,
        "name": "部门1",
        "pid": 0,
        "children": [
            {
                "id": 2,
                "name": "部门2",
                "pid": 1,
                "children": []
            },
            {
                "id": 3,
                "name": "部门3",
                "pid": 1,
                "children": [
                    // 结果 ,,,
                ]
            }
        ]
    }
]
/*
      部门1
      /  \
  部门2   部门3
          /
        部门4
        /
      部门5
*/
```

## 方法一、递归
```js
const transformTree = (data, pid, result) => {
  //遍历当前数组，找到符合条件的节点，推入结果集，然后递归遍历
  for (const key of data) {
    if (key.pid === pid) {
      const resultItem = { ...key, children: [] };
      result.push(resultItem);
      transformTree(data, key.id, resultItem.children)
    }
  }
}
const array2Tree = (data, pid) => {
  const result = [];//结果集
  transformTree(data, pid, result);//转换函数
  return result;
}
let res = array2Tree(arr, 0);
```

## 方法二、使用额外空间存储：牺牲空间换时间
>主要思路是先把数据转成Map去存储，之后遍历的同时借助对象的引用，直接从Map找对应的数据做存储
```js
const array2Tree2 = function (data) {
  //使用map存储
  let result = [];
  let mapObj = {};
  for (const key of data) {
    mapObj[key.id] = { ...key, children: [] }
  }
  for (const key of data) {
    const { id, pid } = key;
    if (pid === 0) {
      result.push(mapObj[id]);
    } else {
      if (!mapObj[pid]) {
        mapObj[pid] = { ...key, children: [] }
      }
      mapObj[pid].children.push(mapObj[id]);
    }
  }
  return result;
}
const res2 = array2Tree2(arr);
```

## 方法三、方法二改进版：一次遍历
>主要思路也是先把数据转成Map去存储，之后遍历的同时借助对象的引用，直接从Map找对应的数据做存储。不同点在遍历的时候即做Map存储,有找对应关系。性能会更好。
```js
const array2Tree3 = function (data) {
  let result = [];
  let mapObj = {};
  for (let key of data) {
    const { id, pid } = key;
    if (!mapObj[id]) {
      mapObj[id] = {
        ...key,
        children: [],
      }
    }
    if (pid === 0) {
      result.push(mapObj[id]);
    } else {
      if (!mapObj[pid]) {
        mapObj[pid] = {
          children: []
        }
      }
      mapObj[pid].children.push(mapObj[id]);
    }
  }
  return result;
}
const res3 = array2Tree3(arr);
```

## 方法四：使用Map
```js
const array2Tree4 = function (data) {
  const result = [];
  const mapObj = new Map();
  for (let key of data) {
    const { id, pid } = key;
    const children = []
    if (!mapObj.get(id)) {
      mapObj.set(id, children)
    }
    if (pid === 0) {
      result.push({ ...key, children });
    } else {
      let arr = mapObj.get(pid);
      arr && arr.push({ ...key, children })
    }
  }
  return result;
}
const res4 = array2Tree4(arr);
```