---
title: 监视属性
date: 2021-10-13 16:19:24
toc: true
---

### 监视属性
1. 当被监视的属性变化时，回调函数自动调用，进行相关操作
2. 监视的属性必须存在，才能进行监视!!(如果不存在对于的属性或计算属性，不会报错)
3. 监视的两种写法
  (1). new Vue时传入watch配置
  (2). 通过vm.$watch监视
```html
<script>
new Vue({
  //第一种方法
  watch: {
    isHot: {
      immediate: true,//初始化时让handler调用一下(默认是false)
      //handler什么时候调用，当isHot发生变化时
      handler(newVal, oldVal) {
        console.log('isHot被修改了: ', newVal, oldVal);
      }
    }
  }
});
//第二种方法
vm.$watch("isHot", {
  immediate: true,
  handler(newVal, oldVal) {
    console.log("isHot被修改了: ", newVal, oldVal);
  },
});
</script>
```

### 深度监视
(1). Vue中的watch默认不监视对象内部值的改变(一层)
(2). 配置`deep：true`可以监测对象内部值改变(多层)
**备注：**
(1). Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以!
(2). 使用watch时根据数据的具体结构，决定是否采用深度监视

### 监视属性简写
```html
<script>
new Vue({
  watch: {
    //正常写法
    isHot: {
      handler(newVal, oldVal) {
        console.log('isHot被修改了: ', newVal, oldVal);
      }
    },
    //简写->只需要配置handler函数
    isHot(newVal, oldVal) {
      console.log('isHot被修改了', newVal, oldVal)
    }
  },
})

// 正常写法
vm.$watch("isHot", {
  handler(newVal, oldVal) {
    console.log("isHot被修改了", newVal, oldVal);
  },
});

// 简写
vm.$watch("isHot", function(newVal,oldVal) {
    console.log("isHot被修改了", newVal, oldVal);
});
</script>
```