---
title: table-表格组件
date: 2020-12-03 16:56:16
tag: Ant Design Pro
---

## 错误-4.x版本

> **Each child in a list should have a unique "key" prop.**

**解决方法**
**来看看官网对rowKey的解释：表格行 key 的取值，可以是字符串或一个函数**
``` js
//在Table组件中加入rowKey属性即可：
<Table columns = { columns } dataSource = { tableData } rowSelection = { rowSelection } rowKey = { row => row.id }/>
//同时每条数据需要有个id：
const data = [{
    id: '1',
    user: 'John Brown',
    address: 'New York No. 1 Lake Park',
}, {
    id: '2',
    user: 'Jim Green',
    address: 'London No. 1 Lake Park',
}, {
    id: '3',
    user: 'Joe Black',
    address: 'Sidney No. 1 Lake Park',
}];
```

## 错误-3.x版本

> **Warning: Each record in table should have a unique `key` prop, or set `rowKey` to an unique primary key. Warning: [antd: Table] Each record in dataSource of table should have a unique `key` prop, or set `rowKey` of Table to an unique primary key, see https://u.ant.design/table-row-key**
**翻译为：**表中的每条记录都应具有唯一的“key”属性，或将“rowKey”设置为唯一的主键。table的dataSource中的每个记录都应该有一个惟一的`key` prop，或者将table的`rowKey`设置为唯一的主键
意思就是：这个报错是说需要对dataSource的行加key，不是对列加key，同时这里的rowKey需要使用行的唯一的属性名，不能使用不唯一的属性名或不存在的属性名，不然还是报那个错。列是否加key均可。
**解决方法**
>同上4.x的解决方法--加rowKey属性
