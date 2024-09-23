---
title: Context代码组织
date: 2020-11-14 20:08:53
tag: Hook
---

前面的案例中，我们在子组件中获取全局状态，都是直接裸用 `useContext`：

```js
import React from 'react'
import { LogStateContext } from './context'

function App() {
  const logs = React.useContext(LogStateContext)
}
```
但是是否有更好的代码组织方法呢？比如这样：
```js
import React from 'react'
import { useLogState } from './context'

function App() {
  const logs = useLogState()
}
```
```js
// context
import React from 'react'

const LogStateContext = React.createContext();

export function useLogState() {
  return React.useContext(LogStateContext)
}

```

在加上点健壮性保证？
```js
import React from 'react'

const LogStateContext = React.createContext();
const LogDispatcherContext = React.createContext();

export function useLogState() {
  const context = React.useContext(LogStateContext)
  if (context === undefined) {
    throw new Error('useLogState must be used within a LogStateProvider')
  }
  return context
}

export function useLogDispatcher() {
  const context = React.useContext(LogDispatcherContext)
  if (context === undefined) {
    throw new Error('useLogDispatcher must be used within a LogDispatcherContext')
  }
  return context
}

```

如果有的组件同时需要读写日志，调用两次很麻烦？
```js
export function useLogs() {
  return [useLogState(), useLogDispatcher()]
}

```

```js
export function App() {
  const [logs, addLogs] = useLogs()
  // ...
}

```