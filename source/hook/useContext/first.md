---
title: 神奇的children
date: 2020-11-10 19:35:10
tag: HookH
---

## 神奇的children
**我们有一个需求，需要通过 Provider 传递一些主题信息给子组件**
```js
import React, { useContext, useState } form "react";
const ThemeContext = React.createContext();

export function ChildNonTheme() {
    console.log('不关心皮肤的子组件渲染');
    return <div>我不关心皮肤，皮肤改变的时候别让我重新渲染！</div>
}

export function ChildWithTheme() {
    const theme = useContext(ThemeContext);
    return <div>我是有皮肤的!{theme}</div>
}

export default function App() {
    const [theme setTheme] = useState("light");
    const onChangeTheme = () => setTheme(theme === "light" ? "dark" : "light");
    return (
        <ThemeContext.Provider value={theme}>
            <button onClick={onChangeTheme}>改变皮肤</button>
            <ChildWithTheme/>
            <ChildNonTheme/>
            <ChildNonTheme/>
            <ChildNonTheme/>
            <ChildNonTheme/>
            <ChildNonTheme/>
        </ThemeContext.Provider>
    )
}
```
这段代码看起来没啥问题，也很符合撸起袖子就干的直觉，但是却会让 `ChildNonTheme` 这个不关心皮肤的子组件，在皮肤状态更改的时候也进行无效的重新渲染。
![图片](/assets/reactImg/1.png "图片")
这本质上是由于` React `是自上而下递归更新，`<ChildNonTheme />` 这样的代码会被 `babel `翻译成 `React.createElement(ChildNonTheme)` 这样的函数调用，`React`官方经常强调` props `是`immutable` 的，所以在每次调用函数式组件的时候，都会生成一份新的 `props `引用。
来看下 `createElement` 的返回结构：
```js
const childNonThemeElement = {
    type: 'ChildNonTheme',
    props: {} //<-这个引用更新了
}
```
正是由于这个新的 `props` 引用，导致` ChildNonTheme` 这个组件也重新渲染了
那么如何避免这个无效的重新渲染呢？关键词是**巧妙利用 children**
```js
import React, { useContext, useState } from "react";

const ThemeContext = React.createContext();

function ChildNonTheme() {
  console.log("不关心皮肤的子组件渲染了");
  return <div>我不关心皮肤，皮肤改变的时候别让我重新渲染！</div>;
}

function ChildWithTheme() {
  const theme = useContext(ThemeContext);
  return <div>我是有皮肤的哦~ {theme}</div>;
}

function ThemeApp({ children }) {
  const [theme, setTheme] = useState("light");
  const onChangeTheme = () => setTheme(theme === "light" ? "dark" : "light");
  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={onChangeTheme}>改变皮肤</button>
      {children}
    </ThemeContext.Provider>
  );
}

export default function App() {
  return (
    <ThemeApp>
      <ChildWithTheme />
      <ChildNonTheme />
      <ChildNonTheme />
      <ChildNonTheme />
      <ChildNonTheme />
      <ChildNonTheme />
      <ChildNonTheme />
      <ChildNonTheme />
    </ThemeApp>
  );
}

```
>没错，唯一的区别就是我把控制状态的组件和负责展示的子组件给抽离开了，通过 `children` 传入后直接渲染，由于 `children` 从外部传入的，也就是说 `ThemeApp` 这个组件内部不会再有 `React.createElement` 这样的代码，那么在 `setTheme` 触发重新渲染后，`children `完全没有改变，所以可以直接复用。
让我们再看一下被 `ThemeApp `包裹下的 `<ChildNonTheme />`，它会作为 `children` 传递给 `ThemeApp`，`ThemeApp` 内部的更新完全不会触发外部的 `React.createElement`，所以会直接复用之前的 `element `结果：
```js
// 完全复用，props 也不会改变。
const childNonThemeElement = {
  type: ChildNonTheme,
  props: {}
}
```
![图片](/assets/reactImg/2.png "图片")
在改变皮肤之后，控制台空空如也！优化达成。
总结下来，就是要把渲染比较费时，但是不需要关心状态的子组件提升到「有状态组件」的外部，作为 `children` 或者`props`传递进去直接使用，防止被带着一起渲染。


作者：ssh_晨曦时梦见兮
链接：https://juejin.im/post/6889247428797530126
来源：掘金