---
title: Collaspe折叠
date: 2020-11-30 16:34:16
tag: Ant Design Pro
---

## 折叠面板控件子控件上点击触发折叠问题
```js
//使用e.stopPropagation();来实现
<xxx onClick={(e) => e.stopPropagation()} />
//或者-在组件外部包一层div，触发div的点击事件来
 <div onClick={(e)=>e.stopPropagation()}><xxx {...props} /></div>
```

