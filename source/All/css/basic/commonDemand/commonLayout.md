---
title: 常用布局
date: 2021-03-30 10:21:20
tog: css
toc: true
---

### 两栏布局(边栏定宽主栏自适应)
#### 方法一：float + overflow（BFC原理）
![41](/assets/cssImg/allBasic/41.png)
#### 方法二：float + margin
![42](/assets/cssImg/allBasic/42.png)
#### 方法三：flex
![43](/assets/cssImg/allBasic/43.png)
#### 方法四：grid
![44](/assets/cssImg/allBasic/44.png)

### 三栏布局(两侧栏定宽主栏自适应)
#### 方法一：圣杯布局
![45](/assets/cssImg/allBasic/45.png)
#### 方法二：双飞翼布局
![46](/assets/cssImg/allBasic/46.png)
#### 方法三：float + overflow（BFC 原理）
![47](/assets/cssImg/allBasic/47.png)
#### 方法四：flex
![48](/assets/cssImg/allBasic/48.png)
#### 方法五：grid
![49](/assets/cssImg/allBasic/49.png)

### 多列登高布局
#### 方法一：padding + 负mrgin
![50](/assets/cssImg/allBasic/50.png)
#### 方法二：设置父级背景图片
![51](/assets/cssImg/allBasic/51.png)

### 三行布局（头尾定高主栏自适应）
```html
<div class="layout">
    <header></header>
    <main>
        <div class="inner"></div>
    </main>
    <footer></footer>
</div>
```
```css
html,
body,
.layout {
    height: 100%;
}
body {
    margin: 0;
}
header, 
footer {
    height: 50px;
}
main {
    overflow-y: auto;
}
```
#### 方法一：calc
![52](/assets/cssImg/allBasic/52.png)
#### 方法二：absolute
![53](/assets/cssImg/allBasic/53.png)
#### 方法三：flex
![54](/assets/cssImg/allBasic/54.png)
#### 方法四：grid
![55](/assets/cssImg/allBasic/55.png)