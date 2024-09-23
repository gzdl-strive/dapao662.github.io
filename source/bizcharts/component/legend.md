---
title: Legend图例
date: 2020-10-29 17:36:55
tag: bizcharts
---

### Legend
**分类图例属性**
>`itemName`：object--描述：配置图例name,其中formatter可格式化图例name 代码示例
>`maxHeight`：number--描述：分类图例适用，图例项最大高度设置。

```js
const LegendItemNameCfg = {
    formatter: function (name) {
        ...
    }
}
<Legend itemName={LegendItemNameCfg} maxItemWidth={0.5} />
```