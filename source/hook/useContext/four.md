---
title: 组合Providers
date: 2020-11-14 20:08:53
tag: Hook
---
假设我们使用上面的办法管理一些全局的小状态，`Provider` 变的越来越多了，有时候会遇到嵌套地狱的情况:
```js
const StateProviders = ({ children }) => (
  <LogProvider>
    <UserProvider>
      <MenuProvider>
        <AppProvider>
          {children}
        </AppProvider>
      </MenuProvider>
    </UserProvider>
  </LogProvider>
)

function App() {
  return (
    <StateProviders>
      <Main />
    </StateProviders>
  )
}

```
有没有办法解决呢？当然有，我们参考 `redux` 中的 `compose` 方法，自己写一个 `composeProvider `方法：
```js
function composeProviders(...providers) {
  return ({ children }) =>
    providers.reduce(
      (prev, Provider) => <Provider>{prev}</Provider>,
      children,
    )
}
```
代码就可以简化成这样：
```js
const StateProviders = composeProviders(
  LogProvider,
  UserProvider,
  MenuProvider,
  AppProvider,
)

function App() {
  return (
    <StateProvider>
      <Main />
    </StateProvider>
  )
}

```

    总结：
    1. 尽量提升渲染无关的子组件元素到「有状态组件」的外部。
    2. 在需要的情况下对 Context 进行读写分离。
    3. 包装Context 的使用，注意错误处理。
    4. 组合多个 Context，优化代码。