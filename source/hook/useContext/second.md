---
title: Context代码组织
date: 2020-11-14 20:08:53
tag: Hook
---

想象一下，现在我们有一个全局日志记录的需求，我们想通过 `Provider` 去做，很快代码就写好了：
```js
import React, { useContext, useState } from "react";
import "./styles.css";

const LogContext = React.createContext();

function LogProvider({ children }) {
  const [logs, setLogs] = useState([]);
  const addLog = (log) => setLogs((prevLogs) => [...prevLogs, log]);
  return (
    <LogContext.Provider value={{ logs, addLog }}>
      {children}
    </LogContext.Provider>
  );
}

function Logger1() {
  const { addLog } = useContext(LogContext);
  console.log('Logger1 render')
  return (
    <>
      <p>一个能发日志的组件1</p>
      <button onClick={() => addLog("logger1")}>发日志</button>
    </>
  );
}

function Logger2() {
  const { addLog } = useContext(LogContext);
  console.log('Logger2 render')
  return (
    <>
      <p>一个能发日志的组件2</p>
      <button onClick={() => addLog("logger2")}>发日志</button>
    </>
  );
}

function LogsPanel() {
  const { logs } = useContext(LogContext);
  return logs.map((log, index) => <p key={index}>{log}</p>);
}

export default function App() {
  return (
    <LogProvider>
      {/* 写日志 */}
      <Logger1 />
      <Logger2 />
      {/* 读日志 */}
      <LogsPanel />
      </div>
    </LogProvider>
  );
}
```
我们已经用上了上一章节的优化小技巧，单独的把 `LogProvider `封装起来，并且把子组件提升到外层传入。
先思考一下最佳的情况，`Logger `组件只负责发出日志，它是不关心`logs`的变化的，在任何组件调用 `addLog` 去写入日志的时候，理想的情况下应该只有 `LogsPanel `这个组件发生重新渲染。
![1](/assets/reactImg/2-1.png "1")
这肯定不是我们预期的，假设在现实场景的代码中，能写日志的组件可多着呢，每次一写入就导致全局的组件都重新渲染？这当然是不能接受的，发生这个问题的本质原因官网 `Context `的部分已经讲得很清楚了：
![2](/assets/reactImg/2-2.png "2")

当` LogProvider `中的` addLog `被子组件调用，导致` LogProvider`重渲染之后，必然会导致传递给 `Provider` 的 `value` 发生改变，由于 `value `包含了 `logs `和 `setLogs` 属性，所以两者中任意一个发生变化，都会导致所有的订阅了 `LogProvider` 的子组件重新渲染。

那么解决办法是什么呢？其实就是**读写分离**，我们把 `logs（读）`和 `setLogs（写）`分别通过不同的 `Provider` 传递，这样负责写入的组件更改了` logs`，其他的「写组件」并不会重新渲染，只有真正关心 `logs` 的「读组件」会重新渲染。

```js
function LogProvider({ children }) {
    const [logs, setLogs] = useState([]);
    const addLog = useCallback((log) => {
        setLogs((prevLogs) => [...prevLogs, log]);
    }, []);
    return (
        <LogDispatcherContext.Provider value={addLog}>
            <LogStateContext.Provider value={logs}>
                { children }
            </LogStateContext.Provider>
        </LogDispatcherContext.Provider>
    )
}

```
我们刚刚也提到，需要保证` value `的引用不能发生变化，所以这里自然要用` useCallback `把` addLog `方法包裹起来，才能保证` LogProvider `重渲染的时候，传递给的`LogDispatcherContext`的`value` 不发生变化。
![3](/assets/reactImg/2-3.png "3")

作者：ssh_晨曦时梦见兮
链接：https://juejin.im/post/6889247428797530126
来源：掘金




