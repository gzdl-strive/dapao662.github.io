---
title: useEffect指南(1)
date: 2020-10-26 10:18:53
tag: Hook
layout: post
toc: true
---

## 分析

### 每个渲染都有自己的props和状态

在我们谈论效果之前，我们需要讨论渲染, 这是一个counter。仔细查看突出显示的行：

``` js
function Counter() {
    const [count, setCount] = useState(0);
    return (
        <div>

    -     <p>You clicked {count} times</p>

        <button onClick={() => setCount(count + 1)}>
            Click me
        </button>
        </div>
    );
}
```

这是什么意思? 是否count以某种方式“监视”状态的更改并自动更新? 当你学习React时，这可能是一个有用的第一直觉，但它不是一个准确的心理模型。
在此示例中，count只是一个数字。 它不是神奇的“数据绑定”，“观察者”，“代理”或其他任何东西。这是一个很古老的数字:

``` javascript
const count = 42; 

// ...

<p>You clicked {count} times</p>

// ...
```

我们的组件第一次渲染时，我们从useState()得到的count变量是0。当我们调用setCount(1)时，React再次调用我们的组件。这一次，count将是1。依此类推：

``` javascript
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
```

每当我们更新状态时，React都会调用我们的组件。每个渲染结果“看到”它自己的counter状态值，这是我们函数内的常量。
所以这一行没有做任何特殊的数据绑定：

 `<p>You clicked {count} times</p>`
它只是在渲染输出中嵌入一个数值。 该数字由React提供。当我们setCount时，React再次使用不同的count值调用我们的组件。然后React更新DOM以匹配我们最新的渲染输出。
关键是，任何特定渲染中的count常量都不会随时间变化。再次调用的是我们的组件——每个渲染都“看到”自己的count值，该值在渲染之间是独立的。

### 每个渲染都有自己的事件处理程序

到现在为止还挺好。那么关于事件处理程序是怎样的？
看看这个例子。它会在三秒钟后显示count的弹框：

``` javascript
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
```

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

``` javascript
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
```

在此示例中，外部 `someone` 变量被重新分配多次。(就像React中的某个地方一样，当前 的组件状态可以改变。) 但是，在 `sayHi` 中，有一个本地 `name` 常量与特定调用中的 `person` 相关联。 那个常量是本地的，所以它在调用之间是独立的！因此，当超时触发时，每个弹框都会“记住”他自己的 `name` 。

这解释了我们的事件处理程序如何在单击时捕获 `count` 。如果我们应用相同的替换原则，每个渲染“看到”它自己的 `count` ：

``` javascript
// During first render
function Counter() {

    - const count = 0; // Returned by useState()  // ...

    function handleAlertClick() {
        setTimeout(() => {
        alert('You clicked on: ' + count);
        }, 3000);
    }
    // ...

}

// After a click, our function is called again
    function Counter() {

    - const count = 1; // Returned by useState()  // ...

    function handleAlertClick() {
        setTimeout(() => {
        alert('You clicked on: ' + count);
        }, 3000);
    }
    // ...

}

// After another click, our function is called again
function Counter() {

    - const count = 2; // Returned by useState()  // ...

    function handleAlertClick() {
        setTimeout(() => {
        alert('You clicked on: ' + count);
        }, 3000);
    }
    // ...

}
```

如此有效，每个渲染都返回自己“版本”的 `handleAlertClick` 。每个版本都“记住”自己的 `count` ：

``` javascript
// During first render
function Counter() {

// ...

function handleAlertClick() {

    setTimeout(() => {

*     alert('You clicked on: ' + 0); 

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

*     alert('You clicked on: ' + 1); 

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

*     alert('You clicked on: ' + 2); 

    }, 3000);

}

// ...

<button onClick={handleAlertClick} /> // The one with 2 inside

// ...

}
```

这就是为什么在这个演示中事件处理程序“属于”特定渲染，当你点击时，它会继续使用该渲染中的counter状态。

**在任何特定渲染中，props和状态永远保持不变**, 但是，如果在渲染之间隔离了props和状态，那么使用它们的任何值(包括事件处理程序)都是独立的。它们也“属于”特定的渲染。因此，即使事件处理程序中的异步函数也会“看到”相同的count值。

旁注：我将具体count值内联到上面的handleAlertClick函数中。这种心理替代是安全的，因为count不可能在特定渲染中改变。它被声明为const并且是一个数字。以同样的方式考虑其他值(比如对象)是安全的，但前提是我们同意避免状态的变化。使用新创建的对象调用setSomething(newObj)而不是对其进行修改，因为属于以前渲染的状态是完整的。

### 每个渲染都有自己的效果(effects)

    让我们回到文档中的一个例子：

``` javascript
function Counter() {
const [count, setCount] = useState(0);

useEffect(() => {
    document.title = `You clicked ${count} times` ;
});

return (
    <div>
    <p>You clicked {count} times</p>
    <button onClick={() => setCount(count + 1)}>
        Click me
    </button>
    </div>
); 

}
```

**这里有一个问题：效果如何读取最新的 `count` 状态？**
也许，有某种“数据绑定”或“观察”使得 `count` 更新在效果函数中生效？也许 `count` 是一个可变变量，React在我们的组件中设置，以便我们的效果总能看到最新值？
不过，我们已经知道 `count` 在特定组件渲染中是恒定(常量)的。事件处理程序从渲染中"查看"”它们“所属”的 `count` 状态，因为 `count` 是它们作用域中的一个变量。效果也是如此！

* `count` 变量不是以某种方式在“不变”效果中发生变化。效果函数本身在每个渲染上都是不同的.

每个版本从其“所属”的渲染中“看到”count值：

``` javascript
// During first render
function Counter() {

// ...

useEffect(
    // 主要是看这里
    // Effect function from first render
    () => {
        document.title = `You clicked ${0} times` ; 
    }
); 

// ...

}

// After a click, our function is called again
function Counter() {

// ...

useEffect(
    // 主要是看这里
    // Effect function from second render
    () => {
        document.title = `You clicked ${1} times` ; 
    }
); 

// ...

}

// After another click, our function is called again
function Counter() {

// ...

useEffect(
    // 主要是看这里
    // Effect function from third render
    () => {
        document.title = `You clicked ${2} times` ; 
    }
); 
// ..

}
```

React会记住你提供的效果函数，刷新对DOM的更改并让浏览器绘制到屏幕后运行该函数。

因此，即使我们在这里谈到单一概念的效果(示例中的更新文档标题( `document.title=''` ))，它也会在每个渲染上用不同的函数 表示 — 并且每个效果函数从它所属的特定渲染中“看到”props和状态。

**从概念上讲，你可以想象效果是 渲染结果的一部分 。**
严格地说，它们不是(为了允许Hook组合而没有笨拙的语法或运行时开销)。但是在我们构建的心理模型中，效果函数属于特定的渲染，就像事件处理程序一样。

-----------------
为了确保我们有一个扎实的理解，让我们回顾一下我们的第一次渲染：

* React: 给我一个当状态为0时UI。
* 你的组件:
    - 这是渲染结果： `<p>You clicked 0 times</p>` 。
    - 还记得在完成后运行此效果： `() => { document.title = 'You * clicked 0 times' }。`
* React: 当然。更新UI。嘿浏览器，我正在向DOM添加一些东西。
* Browser: 很酷，我把它画到了屏幕上。
* React: 好的，现在我要运行你给我的效果了。
    - 运行 `() => { document.title = 'You clicked 0 times' }`
----------------
现在让我们回顾点击后发生的事情：

* 你的组件: 嘿React，把我的状态设置为1。
* React: 给我状态为1时的UI。
* 你的组件:
    - 这是渲染结果： `<p>You clicked 1 times</p>。`
    - 还记得在完成后运行此效果： `() => { document.title = 'You * clicked 1 times' }` 。
* React: 当然。更新UI。嘿浏览器，我改变了DOM。
* Browser: 很酷，我把你的更改画到了屏幕上。
* React: 好的，现在我将运行属于我刚刚做的渲染的效果。
    - 运行 `() => { document.title = 'You clicked 1 times' }。`

### 每个渲染都有它自己....... 一切

**我们现在知道在每次渲染之后运行的效果在概念上是组件输出的一部分，并且“看到”来自该特定渲染的props和状态。**
我们来试试吧。考虑以下代码：

``` javascript
function Counter() {
const [count, setCount] = useState(0);

// 手动高亮
useEffect(() => {

    setTimeout(() => {

    console.log( `You clicked ${count} times` ); 
    }, 3000);
}); 

return (
    <div>
    <p>You clicked {count} times</p>
    <button onClick={() => setCount(count + 1)}>
        Click me
    </button>
    </div>
); 

}
```

如果我稍微点击几次，那么log会是什么样子？
你可能认为这是一个问题，最终结果是不直观的。不是这样的！我们将看到一系列日志 - 每个日志属于特定的渲染，因此具有自己的count值。

![image1](/assets/useEffectImg/1-1.webp "image1")

你可能会想：“当然，这就是它的工作原理！不然它还能怎样工作？“
好吧，这不是 `this.state` 在类上的运作方式。很容易错误地认为这个类的实现是等价的:

``` javascript
componentDidUpdate() {
setTimeout(() => {

    console.log( `You clicked ${this.state.count} times` );

}, 3000); 

}
```

但是， `this.state.count` 始终指向最新计数而不是属于特定渲染的计数。所以你会看到每次记录 `5` ：

![image2](/assets/useEffectImg/1-2.webp "image2")

我认为具有讽刺意味的是，Hook严重依赖于**JavaScript闭包**，然而正是类实现遭受了典型的超时错误值的混淆，而这种混淆通常与闭包相关。这是因为本例中混淆的实际来源是突变 `（react使类中的this.state发生突变，以指向最新状态）` ，而不是闭包本身。

当你相关的值永远不变时，闭包是非常棒的。这使得它们易于思考，因为你实质上是指向常量。 正如我们所讨论的，props和状态永远不会在特定渲染中发生变化。顺便说一句，我们可以通过使用**闭包**修复类版本中的问题。

### 逆势而行(逆流而上)(Swimming Against the Tide)

在这一点上，我们明确地称之为：组件渲染中的**每个**函数(包括事件处理程序、效果、超时或其中的API调用)都会捕获定义它的渲染调用的props和状态。
所以这两个例子是等价的：

``` javascript
function Example(props) {

    useEffect(() => {
        setTimeout(() => {

    -     console.log(props.counter); }, 1000); 

    }); 

// ...

}

function Example(props) {

* const counter = props.counter; 

    useEffect(() => {
        setTimeout(() => {

    -     console.log(counter); }, 1000); 

    }); 

// ...

}
```

无论你是否在组件内部从props或状态中"提前"读取都无关紧要。 他们不会改变！**在单个渲染的作用域内，props和状态保持不变**。（解构props使这更加明显。）
当然，有时你希望在效果中定义的某些回调中读取最新而非捕获的值。最简单的方法是使用refs，如本文最后一节所述。

注意，当你想从 过去 渲染的函数中读取**未来**的props或状态时，你是在逆潮流而上。这并没有错(在某些情况下是必要的)，但打破这种模式可能看起来不那么“干净”。这是一个有意的结果，因为它有助于突出哪些代码是脆弱的，并且依赖于时间而改变。在类上，发生这种情况时不太明显。

这是我们的计数器示例的一个版本，它复制了类的行为：

``` javascript
function Example() {
const [count, setCount] = useState(0);
const latestCount = useRef(count);

    useEffect(() => {
        // Set the mutable latest value
        latestCount.current = count; 
        setTimeout(() => {
        // Read the mutable latest value
        console.log( `You clicked ${latestCount.current} times` ); 
        }, 3000); 
    }); 

// ...

}
```

![image3](/assets/useEffectImg/1-3.webp "计数器-读取最新")

在React中改变一些东西似乎很古怪。但是，这正是React本身在类中重新分配 `this.state` 的方式。与捕获的props和state不同，你无法保证读取 `latestCount.current` 会在任何特定回调中为你提供相同的值。根据定义，你可以随时改变它。这就是为什么它不是默认值，你必须选择它。

### 那么清理呢?

正如文档所解释的那样，某些effect可能会有一个清理阶段。本质上，它的目的是“撤消”订阅等情况的效果。

考虑以下代码：

``` javascript
useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
    return () => {
        ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
    };
});
```

假设props在第一个渲染时为{id：10}，在第二个渲染上为{id：20}。你 可能 认为会发生这样的事情:

* React清除 `{id：10}` 的效果。
* React为 `{id：20}` 渲染UI。
* React运行 `{id：20}` 的效果。

(情况并非如此。)
使用这个心理模型，你可能认为清理“看到”了旧的props，因为它在我们重新渲染之前运行，然后新的效果“看到”了新props，因为它在重新渲染之后运行。这是直接从类生命周期中提取的心智模型，**这里并不准确** 。让我们看看这是为什么。

React仅在让浏览器绘制后运行效果。这使你的应用程序更快，因为大多数效果不需要阻止屏幕更新。效果清理也会延迟。使用新props重新渲染后，前一个效果会被清除：

* React为 `{id：20}` 渲染UI。
* 浏览器绘制画面。我们在屏幕上看到了 `{id：20}` 的用户界面。
* React清除 `{id：10}` 的效果。
* React运行 `{id：20}` 的效果。

你可能想知道：但是如果在props变为 `{id：20}` 之后运行，那么前一效果的清理如何仍能“看到”旧的 `{id：10} props` ？

* 引用上一节：

> 组件渲染中的**每个**函数(包括事件处理程序、效果、超时或其中的API调用)都会捕获定义它的渲染调用的props和状态。

现在答案很明确！不管这意味着什么，效果清理不会读取“最新”的props。它读取属于它定义的渲染的props:

``` javascript
// First render, props are {id: 10}
function Example() {
// ...
useEffect(
    // Effect from first render
    () => {
    ChatAPI.subscribeToFriendStatus(10, handleStatusChange);
    // Cleanup for effect from first render
    // 嘿，你的关注点放在这里(手动高亮)
    return () => {
        ChatAPI.unsubscribeFromFriendStatus(10, handleStatusChange);
    };
    }
);
// ...
}

// Next render, props are {id: 20}
function Example() {
// ...
useEffect(
    // Effect from second render
    () => {
    ChatAPI.subscribeToFriendStatus(20, handleStatusChange);
    // Cleanup for effect from second render
    return () => {
        ChatAPI.unsubscribeFromFriendStatus(20, handleStatusChange);
    };
    }
);
// ...
}
```
但是除了`{id：10}`之外，没有任何东西可以使第一个渲染效果的清理“看到”props。

这就是让React在绘制后立即处理效果的原因——默认情况下让你的应用程序运行得更快。如果我们的代码需要它们，旧的props仍然存在。

### 同步，而不是生命周期

>关于React，我最喜欢的一点是它统一了对初始渲染结果和更新的描述。这会使你的程序降低熵

假设我的组件是这样的：
``` js
function Greeting({ name }) {
  return (
    <h1 className="Greeting">
      Hello, {name}
    </h1>
  );
}
```
如果我渲染`<Greeting name ="Dan"/>`并且后面加上`<Greeting name ="Yuzhi"/>`，或者我只渲染`<Greeting name ="Yuzhi"/>`，都无关紧要。最后，我们将在两种情况下看到`“Hello, Yuzhi”`。

人们常说:**“重要的是过程，而不是目的地。”**而对于React，情况正好相反。**重要的是目的地，而不是旅程。** 这就是jQuery代码中的`$.addClass`和`$.removeClass`调用之间(我们的“旅程”)的区别，并指定了在React代码中CSS类应该是 什么(我们的“目的地”)。

**React根据我们当前的props和状态同步DOM。** 渲染时“mount”或“update”之间没有区别

你应该以类似的方式思考效果。`useEffect`允许你根据我们的props和状态同步React树之外的东西。
```js
function Greeting({ name }) {
  useEffect(() => {
    document.title = 'Hello, ' + name;
  });
  return (
    <h1 className="Greeting">
      Hello, {name}
    </h1>
  );
}
```
这与熟悉的 `mount/update/unmount` 心理模型略有不同。将其内在化是非常重要的。**如果你试图编写一个根据组件是否第一次渲染而表现不同的效果，那么你就是在逆流而上!**如果我们的结果取决于“旅程”而不是“目的地”，我们就无法同步。

无论我们是使用props A，B和C渲染，还是使用C立即渲染，都无关紧要。虽然可能会有一些暂时的差异(例如，在获取数据时)，但最终的结果应该是相同的。

当然，在每个渲染上运行所有效果可能并不是很高效。(在某些情况下，这会导致无限循环。)

那我们怎么解决这个问题呢？

### 教React去Diff你的Effects
>我们已经从DOM本身吸取了教训。React只更新DOM中实际发生更改的部分，而不是在每次重新渲染时都修改它。

- 当你更新
```js
<h1 className="Greeting">
  Hello, Dan
</h1>
```
- 到
```js
<h1 className="Greeting">
  Hello, Yuzhi
</h1>
```
- React看到两个对象
```js
const oldProps = {className: 'Greeting', children: 'Hello, Dan'};
const newProps = {className: 'Greeting', children: 'Hello, Yuzhi'};
```
它检查它们的每个props，并确定子元素已经更改，需要DOM更新，但className没有。所以它可以这样做:
```js
domNode.innerText = 'Hello, Yuzhi';
// No need to touch domNode.className
```
>我们可以用效果做这样的事吗？如果不需要应用效果，最好避免重新运行它们。
例如，由于状态更改，我们的组件可能会重新渲染：
``` js
function Greeting({ name }) {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    document.title = 'Hello, ' + name;
  });

  return (
    <h1 className="Greeting">
      Hello, {name}
      {/* 你的关注点在这里 */}
      {/* 原文此处是count, 实则为counter */}
      <button onClick={() => setCounter(count + 1)}>
        Increment
      </button>
    </h1>
  );
}
```
但是我们的效果不使用`counter`状态。我们的效果将`document.title与name prop`同步，但`name prop`是相同的。 在每次计数器更改时重新分配`document.title`似乎并不理想。

好吧，那么React会不会只是...差异效果`(diff effects)`?
```js
let oldEffect = () => { document.title = 'Hello, Dan'; };
let newEffect = () => { document.title = 'Hello, Dan'; };
// Can React see these functions do the same thing?
```
并不是的。React无法在不调用该函数的情况下猜测该函数的功能。(源代码实际上并不包含特定的值，它只是在`name prop`上结束。)

这就是为什么如果你想避免不必要地重新运行效果，你可以为**useEffect提供一个依赖数组（也称为“deps”）参数**：
```js
seEffect(() => {
  document.title = 'Hello, ' + name;
}, [name]); // Our deps
```
>就像我们告诉React一样：“嘿，我知道你看不到这个函数内部，但我保证它只使用name而不是渲染作用域中的任何其他内容。”

如果这个效果在当前和上次之间的每个值都相同，则无法同步，因此React可以跳过效果：
```js
const oldEffect = () => { document.title = 'Hello, Dan'; };
const oldDeps = ['Dan'];

const newEffect = () => { document.title = 'Hello, Dan'; };
const newDeps = ['Dan'];
```
如果甚至依赖项数组中的一个值在渲染之间是不同的，我们知道运行效果不能被跳过。同步所有的东西！

## 问题总结

### 🤔问题一： 如何使用useEffect替代componentDidMount?

虽然可以用 `useEffect(fn, [])` , 但它并不是完全等价的。与 `componentDidMount` 不同，它将*捕获* `(capture)props` 和状态。所以即使在回调中，你也会看到最初的 `props` 和状态。如果你想看到**最新**的东西，你可以把它写到 `ref` 。但是通常有一种更简单的方法来构造代码，这样你就不必这样做了。请记住，效果 `(effect)` 的心理模型与 `componentDidMount` 和其他生命周期不同，试图找到它们的确切对等物可能会让你感到困惑，而不是有所帮助。为了提高效率，你需要"思考效果", 他们的心智模型更接近于实现同步，而不是响应生命周期事件。

### 🤔问题二： 如何在useEffect中正确获取数据? 什么是[]？

[]表示该效果不使用参与 `React` 数据流的任何值，因此可以安全地应用一次。当实际使用该值时，它也是一个常见的 `bug` 源。你需要学习一些策略(主要是 `useReducer` 和 `useCallback` ), 这些策略可以消除对依赖项的需要，而不是错误的忽略它。

### 🤔问题三：为什么有时会得到一个无限重复获取循环?

如果在没有第二个依赖项参数的情况下在一个效果中执行数据获取，则可能会发生这种情况。没有它，效果会在每次渲染后运行 - 设置状态将再次触发效果。如果指定的这个值始终在依赖关系数组中改变，也可能发生无限循环。你可以通过一个一个地把它们移除来辨别是哪个。但是，删除你使用的依赖项（或盲目指定[]）通常是错误的修复。相反，从源头解决问题。例如，函数可能会导致这个问题，将它们放在效果中、将它们提出来或使用 `useCallback` 包装它们会有所帮助。为避免重新创建对象， `useMemo` 可用于类似目的。

### 🤔问题四：为什么我有时会在效果中获得旧的状态或props值？

效果总是从定义的渲染中“看到” `props` 和状态。这有助于防止错误，但在某些情况下可能会令人讨厌。对于这些情况，你可以显式地在可变 `ref` 中维护一些值。如果你认为你从旧的渲染中看到了一些 `props` 或状态，但并不是你期望的，那么你可能错过了一些依赖项。尝试使用 `lint` 规则来锻炼自己发现它们。过几天，它就会成为你的第二天性。

>下一个篇章--[useEffect指南(2)](/hook/useEffect/second "指南(2)")

作者：xiaohesong
链接：https://www.jianshu.com/p/5e3eca65dd49
来源：简书
备注：以上内容全部来自作者xiaohesong，本文章主要用于自己梳理笔记.
