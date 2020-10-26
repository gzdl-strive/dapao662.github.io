---
title: useEffect指南(1)
date: 2020-10-26 10:18:53

# reward: true

---

## 分析

### 每个渲染都有自己的props和状态

在我们谈论效果之前，我们需要讨论渲染, 这是一个counter。仔细查看突出显示的行：

        function Counter() {
            const [count, setCount] = useState(0);
            return (
                <div>

            +     <p>You clicked {count} times</p>

                <button onClick={() => setCount(count + 1)}>
                    Click me
                </button>
                </div>
            );
        }

这是什么意思? 是否count以某种方式“监视”状态的更改并自动更新? 当你学习React时，这可能是一个有用的第一直觉，但它不是一个准确的心理模型。
在此示例中，count只是一个数字。 它不是神奇的“数据绑定”，“观察者”，“代理”或其他任何东西。这是一个很古老的数字:

    const count = 42; 

    // ...

    <p>You clicked {count} times</p>

    // ...

我们的组件第一次渲染时，我们从useState()得到的count变量是0。当我们调用setCount(1)时，React再次调用我们的组件。这一次，count将是1。依此类推：

    // During first render
    function Counter() {

        const count = 0; // Returned by useState()
        // ...
        <p>You clicked {count} times</p>
        // ...
    }

    // After a click, our function is called again
    function Counter() {
        const count = 1; // Returned by useState()
        // ...
        <p>You clicked {count} times</p>
        // ...
    }

    // After another click, our function is called again
    function Counter() {

        const count = 2; // Returned by useState()
        // ...
        <p>You clicked {count} times</p>
        // ...
    }

每当我们更新状态时，React都会调用我们的组件。每个渲染结果“看到”它自己的counter状态值，这是我们函数内的常量。
所以这一行没有做任何特殊的数据绑定：

    <p>You clicked {count} times</p>

它只是在渲染输出中嵌入一个数值。 该数字由React提供。当我们setCount时，React再次使用不同的count值调用我们的组件。然后React更新DOM以匹配我们最新的渲染输出。
关键是，任何特定渲染中的count常量都不会随时间变化。再次调用的是我们的组件——每个渲染都“看到”自己的count值，该值在渲染之间是独立的。

### 每个渲染都有自己的事件处理程序

到现在为止还挺好。那么关于事件处理程序是怎样的？
看看这个例子。它会在三秒钟后显示count的弹框：

    function Counter() {
        const [count, setCount] = useState(0); 
        function handleAlertClick() {
            setTimeout(() => {
            alert('You clicked on: ' + count);
            }, 3000);
        }

        return (
            <div>
            <p>You clicked {count} times</p>
            <button onClick={() => setCount(count + 1)}>
                Click me
            </button>
            <button onClick={handleAlertClick}>
                Show alert
            </button>
            </div>
        ); 
    }

假设我执行以下步骤：

    增加 计数到3
    点击 “Show alert”
    增加 到5，在timeout触发之前

你希望弹出框显示什么？它会显示 `5` - 这是弹框时的计数器状态吗？或者它会显示 `3` - 我点击时的状态？
如果这种行为对你没有意义，想象一个更实际的例子：一个聊天应用程序，当前收件人 `ID` 在状态中，并有一个发送按钮。这篇文章深入探讨了原因，但正确的答案是 `3` 。
弹框 `(alert)` 将在我单击按钮时**捕获**状态。
(有一些方法可以实现其他行为，但我现在将关注默认情况。当我们建立一个心理模型时，重要的是我们要区分“最小阻力路径”和选择进入逃生舱。)
但它是如何工作的？
我们已经讨论过 `count` 值对于我们函数的每个特定调用都是常量。值得强调的是这一点 — 我们的函数被调用了很多次(每次渲染一次)，但是每次调用的 `count` 值都是常量，并被设置为一个特定的值(该渲染的状态)。
这不是React所特有的 - 常规函数以类似的方式工作：

    function sayHi(person) {
        const name = person.name; setTimeout(() => {
            alert('Hello, ' + name);
        }, 3000); 
    }

    let someone = {name: 'Dan'}; 
    sayHi(someone); 

    someone = {name: 'Yuzhi'}; 
    sayHi(someone); 

    someone = {name: 'Dominic'}; 
    sayHi(someone); 

在此示例中，外部 `someone` 变量被重新分配多次。(就像React中的某个地方一样，当前 的组件状态可以改变。) 但是，在 `sayHi` 中，有一个本地 `name` 常量与特定调用中的 `person` 相关联。 那个常量是本地的，所以它在调用之间是独立的！因此，当超时触发时，每个弹框都会“记住”他自己的 `name` 。

这解释了我们的事件处理程序如何在单击时捕获 `count` 。如果我们应用相同的替换原则，每个渲染“看到”它自己的 `count` ：

    // During first render
    function Counter() {

        * const count = 0; // Returned by useState()  // ...

        function handleAlertClick() {
            setTimeout(() => {
            alert('You clicked on: ' + count);
            }, 3000);
        }
        // ...
    }

    // After a click, our function is called again
        function Counter() {

        * const count = 1; // Returned by useState()  // ...

        function handleAlertClick() {
            setTimeout(() => {
            alert('You clicked on: ' + count);
            }, 3000);
        }
        // ...
    }

    // After another click, our function is called again
    function Counter() {

        * const count = 2; // Returned by useState()  // ...

        function handleAlertClick() {
            setTimeout(() => {
            alert('You clicked on: ' + count);
            }, 3000);
        }
        // ...
    }

如此有效，每个渲染都返回自己“版本”的 `handleAlertClick` 。每个版本都“记住”自己的 `count` ：

    // During first render
    function Counter() {

    // ...

    function handleAlertClick() {

        setTimeout(() => {

    -     alert('You clicked on: ' + 0); 

        }, 3000);

    }

    // ...

    <button onClick={handleAlertClick} /> // The one with 0 inside

    // ...

    }

    // After a click, our function is called again
    function Counter() {

    // ...

    function handleAlertClick() {

        setTimeout(() => {

    -     alert('You clicked on: ' + 1); 

        }, 3000);

    }

    // ...

    <button onClick={handleAlertClick} /> // The one with 1 inside

    // ...

    }

    // After another click, our function is called again
    function Counter() {

    // ...

    function handleAlertClick() {

        setTimeout(() => {

    -     alert('You clicked on: ' + 2); 

        }, 3000);

    }

    // ...

    <button onClick={handleAlertClick} /> // The one with 2 inside

    // ...

    }
这就是为什么在这个演示中 事件处理程序“属于”特定渲染，当你点击时，它会继续使用 该 渲染中的counter状态。

在任何特定渲染中，props和状态永远保持不变。 但是，如果在渲染之间隔离了props和状态，那么使用它们的任何值(包括事件处理程序)都是独立的。它们也“属于”特定的渲染。因此，即使事件处理程序中的异步函数也会“看到”相同的count值。

旁注：我将具体count值内联到上面的handleAlertClick函数中。这种心理替代是安全的，因为count不可能在特定渲染中改变。它被声明为const并且是一个数字。以同样的方式考虑其他值(比如对象)是安全的，但前提是我们同意避免状态的变化。使用新创建的对象调用setSomething(newObj)而不是对其进行修改，因为属于以前渲染的状态是完整的。

## 问题总结

### 🤔问题一： 如何使用useEffect替代componentDidMount?

虽然可以用 `useEffect(fn, [])` , 但它并不是完全等价的。与 `componentDidMount` 不同，它将*捕获* `(capture)props` 和状态。所以即使在回调中，你也会看到最初的 `props` 和状态。如果你想看到**最新**的东西，你可以把它写到 `ref` 。但是通常有一种更简单的方法来构造代码，这样你就不必这样做了。请记住，效果 `(effect)` 的心理模型与 `componentDidMount` 和其他生命周期不同，试图找到它们的确切对等物可能会让你感到困惑，而不是有所帮助。为了提高效率，你需要"思考效果", 他们的心智模型更接近于实现同步，而不是响应生命周期事件。

### 🤔问题二： 如何在useEffect中正确获取数据? 什么是[]？

[]表示该效果不使用参与 `React` 数据流的任何值，因此可以安全地应用一次。当实际使用该值时，它也是一个常见的 `bug` 源。你需要学习一些策略(主要是 `useReducer` 和 `useCallback` ), 这些策略可以消除对依赖项的需要，而不是错误的忽略它。

### 🤔问题三：为什么有时会得到一个无限重复获取循环?

如果在没有第二个依赖项参数的情况下在一个效果中执行数据获取，则可能会发生这种情况。没有它，效果会在每次渲染后运行 - 设置状态将再次触发效果。如果指定的这个值始终在依赖关系数组中改变，也可能发生无限循环。你可以通过一个一个地把它们移除来辨别是哪个。但是，删除你使用的依赖项（或盲目指定[]）通常是错误的修复。相反，从源头解决问题。例如，函数可能会导致这个问题，将它们放在效果中、将它们提出来或使用 `useCallback` 包装它们会有所帮助。为避免重新创建对象， `useMemo` 可用于类似目的。

### 🤔问题四：为什么我有时会在效果中获得旧的状态或props值？

效果总是从定义的渲染中“看到” `props` 和状态。这有助于防止错误，但在某些情况下可能会令人讨厌。对于这些情况，你可以显式地在可变 `ref` 中维护一些值。如果你认为你从旧的渲染中看到了一些 `props` 或状态，但并不是你期望的，那么你可能错过了一些依赖项。尝试使用 `lint` 规则来锻炼自己发现它们。过几天，它就会成为你的第二天性。

作者：xiaohesong
链接：https://www.jianshu.com/p/5e3eca65dd49
来源：简书
备注：以上内容全部来自作者xiaohesong，本文章主要用于自己梳理笔记.
