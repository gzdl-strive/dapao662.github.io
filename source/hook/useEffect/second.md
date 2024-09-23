---
title: useEffect指南(2)
date: 2020-10-26 10:18:53
tag: Hook
toc: true
comments: true
---

## 不要因为依赖而撒谎

在依赖关系上撒谎会带来不好的后果。从直觉上看, 这是有道理的, 但我看到几乎每个人都尝试使用 `useEffect` 与类中的心理模型试图欺骗规则。(一开始我也这么做了!)

``` js
function SearchResults() {
    async function fetchData() {
        // ...
    }

    useEffect(() => {
        fetchData();
    }, []); // Is this okay? Not always -- and there's a better way to write it.

    // ...
}
```

([Hook FAQ](https://reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)解释了应该怎么做。我们将在下面回到这个例子。)
"但我只想在mount时运行它！"，你会这么说。现在，请记住：如果你指定 `deps` ，组件内部的效果所使用的所有值都必须在那里。包括**props，状态，函数 - 组件中的任何内容**。
有时，当你这样做时，它会导致问题。例如，你可能会看到一个无限重取循环，或者一个socket被重新创建得太频繁。 **该问题的解决方案不是删除依赖项。** 我们很快就会看到这些解决方案。

但在我们跳到解决方案之前，让我们更好地理解问题。

## 当依赖关系欺骗时会发生什么

如果 `deps` (依赖项)包含了效果使用的所有值，React知道什么时候重新运行它:

``` js
useEffect(() => {
    document.title = 'Hello, ' + name;
}, [name]); // 关注这个依赖项
```

![image1](/assets/useEffectImg/2-1.webp "image1")

**(依赖关系是不同的，所以我们重新运行效果。)**

但是，如果我们为此效果指定了[]，则新效果函数将不会运行：

``` js
useEffect(() => {
    document.title = 'Hello, ' + name;
}, []); // Wrong: name is missing in deps
```

![image2](/assets/useEffectImg/2-2.webp "image2")

**(依赖项是相同的，所以我们跳过这个效果。)**

在这种情况下，问题可能显而易见。但是，在其他情况下，当类解决方案从你的记忆中“跳出”时，这种直觉可能会欺骗你。

例如，假设我们正在编写一个每秒递增一次的计数器。对于类，我们的直觉是：“设置间隔( `interval` )一次，销毁间隔一次”。这是一个我们如何做的例子。当我们在心理上将此代码翻译为 `useEffect` 时，我们本能地将[]添加到deps中。“我想让它跑一次”，对吗？

``` js
function Counter() {
    const [count, setCount] = useState(0);

    useEffect(() => {
        const id = setInterval(() => {
            setCount(count + 1);
        }, 1000);
        return () => clearInterval(id);
    }, []); //你的关注点在这里

    return <h1 > {
        count
    } < /h1>;
}
```

但是，此示例**仅递增一次**。哎呀。

如果你的心理模型是“依赖关系让我指定何时我想重新触发效果”，这个例子可能会给你一个存在的危机。你想要触发它一次，因为它是一个间隔 - 所以它为什么会引起问题？

但是，**如果你知道依赖项是我们的提示，用于对效果在渲染范围中使用的所有内容作出反应，那么这样做是有意义的。**它使用 `count` ，但我们使用[]撒谎说它没有依赖。它会坑到我们，这只是时间问题!

在第一次渲染中， `count` 为0. 因此，第一个渲染效果中的 `setCount(count + 1)` 表示 `setCount(0 + 1)` 。因为 `[] deps` 我们永远不会重新运行效果，所以它会每秒调用 `setCount(0 + 1)` ：

``` js
// First render, state is 0
function Counter() {
    // ...
    useEffect(
        // Effect from first render
        () => {
            const id = setInterval(() => {
                +setCount(0 + 1); // Always setCount(1)
            }, 1000);
            return () => clearInterval(id);
        },
        +[] // Never re-runs
    );
    // ...
}

// Every next render, state is 1
function Counter() {
    // ...
    useEffect(

        + // This effect is always ignored because
        + // we lied to React about empty deps.

        () => {
            const id = setInterval(() => {
                setCount(1 + 1);
            }, 1000);
            return () => clearInterval(id);
        },
        []
    );
    // ...
}
```

**我们撒谎说我们的效果不依赖于组件内部的值，而实际上它依赖于组件内部的值!**
我们的效果使用 `count` - 组件内部的值（但在effect效果之外）：

``` js
+
const count = // ...

    useEffect(() => {
        const id = setInterval(() => {
            +setCount(count + 1);
        }, 1000);
        return () => clearInterval(id);
    }, []);
```

因此，将[]指定为依赖项将产生一个错误。React将比较依赖项，并跳过更新此效果：

![image3](/assets/useEffectImg/2-3.webp "image3")

**(依赖性是相同的，所以我们跳过这个效果。)**
像这样的问题很难想到。因此，我鼓励你将其作为一项硬性规则，始终诚实地遵循效果依赖关系，并将其全部(效果所用到的)指定。(如果你想在团队中强制执行此操作，我们会提供一个 `[lint规则](lint rule)` 。)

## 诚实对待依赖关系的两种方法

有两种策略可以诚实地对待依赖关系。通常应该从第一个开始，然后根据需要应用第二个。
*第一种策略*是**修复依赖关系数组**，以包含在效果中使用的组件内的所有值。我们将 `count` 作为 `dep` (依赖)包括：

``` js
useEffect(() => {
    const id = setInterval(() => {
        setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
}, [count]);
```

这使依赖项数组正确。它可能不太理想，但这是我们需要解决的第一个问题。现在改变 `count` 将重新运行效果，每一个下一个间隔引用 `count` 从其渲染在 `setCount(count + 1)` :

``` js
// First render, state is 0
function Counter() {
    // ...
    useEffect(
        // Effect from first render
        () => {
            const id = setInterval(() => {
                +setCount(0 + 1); // setCount(count + 1)
            }, 1000);
            return () => clearInterval(id);
        },
        +[0] // [count]
    );
    // ...
}

// Second render, state is 1
function Counter() {
    // ...
    useEffect(
        // Effect from second render
        () => {
            const id = setInterval(() => {
                +setCount(1 + 1); // setCount(count + 1)
            }, 1000);
            return () => clearInterval(id);
        },
        +[1] // [count]
    );
    // ...
}
```

这样可以解决问题，但只要 `count` 发生变化，我们的间隔就会被清除并再次设置。这可能是不受欢迎的：

![image4](/assets/useEffectImg/2-4.webp "image4")

**(依赖关系是不同的，所以我们重新运行效果。)**

*第二种策略*是**更改我们的effect代码**，这样它就不需要比我们想要的值频繁的更改。 我们不想在依赖关系上撒谎——我们只是想改变我们的效果，使依赖关系更少。

让我们看一些删除依赖项的常用技巧。

### 使效果自给自足

我们希望摆脱效果中的 `count` 依赖性。

``` js
useEffect(() => {
    const id = setInterval(() => {
        setCount(count + 1);
    }, 1000);
    return () => clearInterval(id);
}, [count]);
```

要做到这一点，我们需要问自己：我们使用 `count` 用来做什么？ 好像我们只将它用于 `setCount` 调用。在这种情况下，**我们实际上在作用域里根本不需要 `count` 。**当我们想要根据以前的状态更新状态时，我们可以使用 `setState` 的函数更新形式：

``` js
useEffect(() => {
    const id = setInterval(() => {
        // 你的关注点在这里
        setCount(c => c + 1);
    }, 1000);
    return () => clearInterval(id);
}, []);
```

我喜欢将这些情况视为“错误(假的)依赖”。是的， `count` 是一个必要的依赖项，因为我们在效果中编写了 `setCount(count + 1)` 。但是，我们真正的需要 `count` 只是去将其转换为 `count + 1` 并将其“发回”给React。但是React已经知道了当前的 `count` 。**我们需要告诉React的是增加这个状态——不管它现在是什么状态。**
这正是 `setCount(c => c + 1)` 的作用。你可以将其看作是向React“发送一条指令”来对状态应该如何变化做出反应。这种“更新形式”在其他情况下也有帮助，例如*批量多次更新时*。

请注意，我们实际上已经完成了删除依赖项的工作。我们没有欺骗他。我们的效果不再从渲染作用域读取 `count` 值：

![image5](/assets/useEffectImg/2-5.webp "image5")

**(依赖项是相同的，所以我们跳过这个效果。)**
即使此效果仅运行一次，属于第一个渲染的间隔回调完全能够在每次间隔触发时发送 `c => c + 1` 更新指令。它不再需要知道当前的计数器状态。 React已经知道了。

### 功能更新和Google文档

还记得我们如何谈论同步是效果的心理模型吗？同步的一个有趣方面是，你经常希望保持系统之间的“消息”与其状态无关。例如，在Google Docs中编辑文档实际上并不会将整个页面发送到服务器。那将是非常低效的。相反，它会发送用户尝试执行操作的表示。

虽然我们的用例是不同的，但是类似的哲学适用于效果。**它只有助于将最少的必要信息从效果内部发送到组件。** 像 `setCount(c => c + 1)` 这样的更新形式传递的信息严格少于 `setCount(count + 1)` ，因为它没有被当前计数 `(count)` “污染”。它只表示动作(“递增”)。在React中思考涉及到找到最小的状态。这是相同的原则，但对于更新。
编码意图(而不是结果)类似于谷歌文档解决协作编辑的方式。虽然这是延伸的类比，但功能更新在React中也扮演着类似的角色。它们确保来自多个源的更新(事件处理程序、效果订阅等)能够以可预测的方式正确地应用于批处理中。
**但是，即使是 `setCount(c => c + 1)` 也不是那么好。** 它看起来有点奇怪，它能做的非常有限。例如，如果我们有两个状态变量，它们的值相互依赖，或者如果我们需要根据prop来计算下一个状态，这对我们没有帮助。幸运的是，setCount(c => c + 1)有一个更强大的姐妹模式。它的名称是useReducer。

### 将更新与操作解耦

让我们修改前面的例子，让它有两个状态变量： `count` 和 `step` 。我们的间隔将使 `count` 增加 `step` 输入的值：

``` js
function Counter() {
    const [count, setCount] = useState(0);
    const [step, setStep] = useState(1);

    useEffect(() => {
        const id = setInterval(() => {
            setCount(c => c + step);
        }, 1000);
        return () => clearInterval(id);
    }, [step]);

    return ( <>
        <h1> { count } </h1> 
        <input value = { step }
        onChange = {
            e => setStep(Number(e.target.value))
        }/> 
        </>
    );
}
```

请注意，**我们没有欺骗。** 由于我开始在效果中使用 `step` ，我将它添加到依赖项中。这就是代码正确运行的原因。
此示例中的当前行为是更改 `step` 重新启动间隔 - 因为它是其中一个依赖项。在许多情况下，这正是你想要的！拆除一个效果并重新设置它没有错，除非我们有很好的理由，否则我们不应该避免这样做。

但是，假设我们希望间隔时钟不会在 `step` 更改时重置。**我们如何从效果中删除step依赖？**
**设置状态变量时，取决于另一个状态变量的当前值，你可能希望尝试使用useReducer替换它们。**
当你发现自己正在编写 `setSomething(something =>…)` 时，是时候考虑使用 `reducer` 了。 `reducer` 允许你 **将表示组件中发生的“操作”与状态更新的响应方式解耦。**
在我们的效果中，让我们将 `step` 依赖关系替换为 `dispatch` 依赖关系:

``` js
const [state, dispatch] = useReducer(reducer, initialState);
const {
    count,
    step
} = state;

useEffect(() => {
    const id = setInterval(() => {
        dispatch({
            type: 'tick'
        }); // Instead of setCount(c => c + step);
    }, 1000);
    return () => clearInterval(id);
}, [dispatch]);
```

你可能会问我：“这怎么会更好？”答案是**React保证dispatch函数在整个组件生命周期内保持不变。所以上面的例子不需要重新订阅间隔**
我们解决了问题！
*(你可以省略deps中的dispatch，setState和useRef容器值，因为React保证它们是静态的。但指定它们也没有坏处。)*
它不是在效果中读取状态，而是发送一个动作来编码*发生了什么*的信息。这允许我们的效果保持与 `step` 状态解耦。我们的效果并不关心我们如何更新状态，它只是告诉我们发生了什么。

reducer集中了更新逻辑：

``` js
const initialState = {
    count: 0,
    step: 1,
};

function reducer(state, action) {
    const {
        count,
        step
    } = state;
    if (action.type === 'tick') {
        return {
            count: count + step,
            step
        };
    } else if (action.type === 'step') {
        return {
            count,
            step: action.step
        };
    } else {
        throw new Error();
    }
}
```

**(如果你之前错过了，那么这是一个演示)**

### 为什么useReducer是Hooks的欺骗模式

我们已经看到当效果需要根据以前的状态或另一个状态变量设置状态时如何删除依赖关系。但是如果我们需要 `props` 来计算下一个状态呢？ 例如，我们的 `API` 可能是 `<Counter step = {1} />` 。当然，在这种情况下，我们不能避免将 `props.step` 指定为依赖项？
事实上，我们可以！我们可以将 `reducer` 本身 放在我们的组件中来读取 `props` ：

``` js
function Counter({
    step
}) {
    const [count, dispatch] = useReducer(reducer, 0);

    function reducer(state, action) {
        if (action.type === 'tick') {
            // 你的关注点应该在这里
            return state + step;
        } else {
            throw new Error();
        }
    }

    useEffect(() => {
        const id = setInterval(() => {
            dispatch({
                type: 'tick'
            });
        }, 1000);
        return () => clearInterval(id);
    }, [dispatch]);

    return <h1 > {
        count
    } < /h1>;
}
```

这个模式禁用了一些优化，所以尽量不要在任何地方都使用它，但是如果需要，你可以完全从reducer访问props。
**即使在这种情况下，重新渲染之间仍然保证dispatch标识是稳定的。** 因此，如果需要，你可以从效果deps中省略它。它不会导致重新运行效果。
你可能想知道：这怎么可能有效呢？当从属于另一个渲染效果的内部调用时， `reducer` 如何“知道” `props` ? 答案是，当你 `dispatch` 时，React会记住该操作 - 但它会在下一次渲染期间调用你的 `reducer` 。在那时，新的props将在作用域内，你不会在一个效果内。
**这就是为什么我喜欢将useReducer视为Hooks的“作弊(欺骗)模式”。它让我将更新逻辑从描述所发生的事情中解耦出来。反过来，这有助于我从我的效果中删除不必要的依赖项，并避免更频繁地重新运行它们。**

### 移动函数到效果内

一个常见的错误是认为函数不应该是依赖关系。例如，这似乎可以工作：

``` js
function SearchResults() {
    const [data, setData] = useState({
        hits: []
    });

    async function fetchData() {
        const result = await axios(
            'https://hn.algolia.com/api/v1/search?query=react',
        );
        setData(result.data);
    }

    useEffect(() => {
        fetchData();
    }, []); // Is this okay?
    // ...
```

需要明确的是，此代码确实有效。**但是简单地忽略局部函数的问题是，当组件增长时，很难判断我们是否在处理所有的情况!**
想象一下，我们的代码是这样拆分的，每个函数都要大(很长，不易维护)五倍：

``` js
function SearchResults() {
    // Imagine this function is long
    function getFetchUrl() {
        return 'https://hn.algolia.com/api/v1/search?query=react';
    }

    // Imagine this function is also long
    async function fetchData() {
        const result = await axios(getFetchUrl());
        setData(result.data);
    }

    useEffect(() => {
        fetchData();
    }, []);

    // ...
}
```

现在让我们说我们稍后在其中一个函数中使用一些状态或prop：

``` js
function SearchResults() {
    const [query, setQuery] = useState('react');

    // Imagine this function is also long
    function getFetchUrl() {
        +
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }

    // Imagine this function is also long
    async function fetchData() {
        const result = await axios(getFetchUrl());
        setData(result.data);
    }

    useEffect(() => {
        fetchData();
    }, []);

    // ...
}
```

如果我们忘记更新调用这些函数的任何效果的deps(可能通过其他函数！)，我们的效果将无法同步来自 `props` 和 `state` 的更改。这听起来不太好。
幸运的是，这个问题有一个简单的解决方案。**如果你只在效果中使用某些函数，请将它们直接移动到该效果中：**

``` js
function SearchResults() {
    // ...
    useEffect(() => {
        // 你的关注点在这里
        // We moved these functions inside!
        function getFetchUrl() {
            return 'https://hn.algolia.com/api/v1/search?query=react';
        }
        async function fetchData() {
            const result = await axios(getFetchUrl());
            setData(result.data);
        }

        fetchData();
    }, []); // ✅ Deps are OK
    // ...
}
```

那么有什么好处呢？我们不再需要考虑“传递依赖”。我们的依赖数组不再欺骗你: **我们真的没有在我们的效果中使用来自组件外部作用域的任何东西。**
如果我们稍后修改 `getFetchUrl` 以使用query状态，我们更有可能注意到我们在效果中编辑它 - 因此，我们需要向效果依赖项添加query：

``` js
function SearchResults() {
    const [query, setQuery] = useState('react');

    useEffect(() => {
        function getFetchUrl() {
            return 'https://hn.algolia.com/api/v1/search?query=' + query;
        }

        async function fetchData() {
            const result = await axios(getFetchUrl());
            setData(result.data);
        }

        fetchData();
    }, [query]); // ✅ Deps are OK

    // ...
}
```

通过添加此依赖项，我们不仅仅是“安抚React”。当 `query` 发生更改时，重新获取数据是有意义的。
`useEffect` **的设计迫使你注意到我们的数据流中的变化，并选择我们的效果应该如何同步它——而不是忽略它，直到我们的产品用户遇到bug**
感谢 `eslint-plugin-react-hooks` 插件中的 `exhaustive-deps` lint规则，你可以在编辑器中输入时分析效果，并获得有关缺少哪些依赖项的建议。换句话说，计算机可以告诉你组件未正确处理哪些数据流更改。

![image6](/assets/useEffectImg/2-6.webp "image6")

### 但我不能把这个函数放在一个效果中

有时你可能不想把函数移到效果中。例如，同一组件中的多个效果可能会调用相同的函数，并且你不希望复制和粘贴其逻辑。或许这是一个prop。
你应该在效果依赖中跳过这样的函数吗？我想不是。同样，**效果不应该在依赖关系上撒谎。** 通常有更好的解决方案。一个常见的误解是“一个函数永远不会改变”。但正如我们在整篇文章中所学到的，这不可能是事实。实际上，组件内定义的函数会在每个渲染中发生变化！
**这本身就是一个问题。** 假设两个效果调用 `getFetchUrl` ：

``` js
function SearchResults() {
    function getFetchUrl(query) {
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }

    useEffect(() => {
        const url = getFetchUrl('react');
        // ... Fetch data and do something ...
    }, []); // 🔴 Missing dep: getFetchUrl

    useEffect(() => {
        const url = getFetchUrl('redux');
        // ... Fetch data and do something ...
    }, []); // 🔴 Missing dep: getFetchUrl

    // ...
}
```

在这种情况下，你可能不希望在任何一个效果中移动 `getFetchUrl` ，因为这样你就无法共享逻辑。
另一方面，如果你对效果依赖性“诚实”，则可能会遇到问题。由于我们的效果都依赖于 `getFetchUrl` （每个渲染都不同 ），我们的依赖数组是无用的：

``` js
function SearchResults() {
    // 🔴 Re-triggers all effects on every render
    // 你的关注点在这里
    function getFetchUrl(query) {
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }

    useEffect(() => {
        const url = getFetchUrl('react');
        // ... Fetch data and do something ...
    }, [getFetchUrl]); // 🚧 Deps are correct but they change too often

    useEffect(() => {
        const url = getFetchUrl('redux');
        // ... Fetch data and do something ...
    }, [getFetchUrl]); // 🚧 Deps are correct but they change too often

    // ...
}
```

一个诱人的解决方案是直接跳过 `deps` 列表中的 `getFetchUrl` 函数。但是，我不认为这是一个很好的解决方案。这使我们很难注意到何时向数据流添加需要由效果处理的更改。这会导致我们之前看到的“永不更新间隔 `(interval)` ”等错误。

相反，还有另外两种更简单的解决方案。
**首先，如果函数不使用组件范围中的任何内容，你可以将其提升到组件外部，然后在效果中自由使用它：**

``` js
// ✅ Not affected by the data flow
function getFetchUrl(query) {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
}

function SearchResults() {
    useEffect(() => {
        const url = getFetchUrl('react');
        // ... Fetch data and do something ...
    }, []); // ✅ Deps are OK

    useEffect(() => {
        const url = getFetchUrl('redux');
        // ... Fetch data and do something ...
    }, []); // ✅ Deps are OK

    // ...
} // ✅ Not affected by the data flow
function getFetchUrl(query) {
    return 'https://hn.algolia.com/api/v1/search?query=' + query;
}

function SearchResults() {
    useEffect(() => {
        const url = getFetchUrl('react');
        // ... Fetch data and do something ...
    }, []); // ✅ Deps are OK

    useEffect(() => {
        const url = getFetchUrl('redux');
        // ... Fetch data and do something ...
    }, []); // ✅ Deps are OK

    // ...
}
```

**无需在 `deps` 中指定它，因为它不在渲染范围内，并且不受数据流的影响。它不能偶然地依赖于 `props` 或状态。**

**或者，你可以将其包装到useCallback Hook中：**

``` js
function SearchResults() {
    // ✅ Preserves identity when its own deps are the same
    // 你的关注点在这里
    const getFetchUrl = useCallback((query) => {
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }, []); // ✅ Callback deps are OK

    useEffect(() => {
        const url = getFetchUrl('react');
        // ... Fetch data and do something ...
    }, [getFetchUrl]); // ✅ Effect deps are OK

    useEffect(() => {
        const url = getFetchUrl('redux');
        // ... Fetch data and do something ...
    }, [getFetchUrl]); // ✅ Effect deps are OK

    // ...
}
```

`useCallback` 实际上就像添加另一层依赖性检查。它正在另一端解决问题 — **我们只在必要时更改函数本身，而不是避免函数依赖。**
让我们看看为什么这种方法很有用。以前，我们的示例显示了两个搜索结果（针对 `'react'` 和 `'redux'` 搜索查询）。但是，假设我们要添加一个输入，以便可以搜索任意 `query` 。因此，getFetchUrl现在不会将 `query` 作为参数，而是从本地状态读取它。
我们会立即看到它缺少 `query` 依赖项：

``` js
function SearchResults() {
    const [query, setQuery] = useState('react');
    const getFetchUrl = useCallback(() => { // No query argument
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }, []); // 🔴 Missing dep: query
    // ...
}
```

如果我修复了 `useCallback deps` 以包含 `query` ，那么每当 `query` 更改时， `deps` 中的 `getFetchUrl` 的任何效果都会重新运行：

``` js
function SearchResults() {
    const [query, setQuery] = useState('react');

    // ✅ Preserves identity until query changes
    const getFetchUrl = useCallback(() => {
        return 'https://hn.algolia.com/api/v1/search?query=' + query;
    }, [query]); // ✅ Callback deps are OK

    useEffect(() => {
        const url = getFetchUrl();
        // ... Fetch data and do something ...
    }, [getFetchUrl]); // ✅ Effect deps are OK

    // ...
}
```

感谢 `useCallback` ，如果 `query` 相同，则 `getFetchUrl` 也保持不变，并且我们的效果不会重新运行。但是如果 `query` 更改， `getFetchUrl` 也会更改，我们将重新获取数据。这很像在 `Excel` 电子表格中更改某些单元格时，使用它的其他单元格会自动重新计算。
这只是拥抱数据流和同步思维的结果。**相同的解决方案适用于从父级传递的函数props：**

``` js
function Parent() {
    const [query, setQuery] = useState('react');

    // 你的关注点在这里
    // ✅ Preserves identity until query changes
    const fetchData = useCallback(() => {
        const url = 'https://hn.algolia.com/api/v1/search?query=' + query;
        // ... Fetch data and return it ...
    }, [query]); // ✅ Callback deps are OK

    return <Child fetchData = {
        fetchData
    }
    />
}

function Child({
    fetchData
}) {
    let [data, setData] = useState(null);

    useEffect(() => {
        fetchData().then(setData);
    }, [fetchData]); // ✅ Effect deps are OK

    // ...
}
```

由于 `fetchData` 只在 `query` 状态发生变化时才会在 `Parent` 内部发生变化，所以我们的 `Child` 在应用程序真正需要时才会重新获取数据。

### 函数是数据流的一部分吗？

有趣的是，用类打破了这种模式，真正显示了效果和生命周期范例之间的差异。考虑一下这个转换:

``` js
class Parent extends Component {
    state = {
        query: 'react'
    };
    // 你的关注点在这里(手动高亮)
    fetchData = () => {
        const url = 'https://hn.algolia.com/api/v1/search?query=' + this.state.query;
        // ... Fetch data and do something ...
    };
    render() {
        return <Child fetchData = {
            this.fetchData
        }
        />;
    }
}

class Child extends Component {
    state = {
        data: null
    };
    componentDidMount() {
        this.props.fetchData();
    }
    render() {
        // ...
    }
}
```

你可能会想：“得了吧, 我们都知道 `useEffect` 就像 `componentDidMount` 和 `componentDidUpdate` 结合在一起，你无法继续再摇旗呐喊了！” **然而，即使使用componentDidUpdate，这也不起作用：**

``` js
class Child extends Component {
    state = {
        data: null
    };
    componentDidMount() {
        this.props.fetchData();
    }
    componentDidUpdate(prevProps) {
        // 🔴 This condition will never be true
        if (this.props.fetchData !== prevProps.fetchData) {
            this.props.fetchData();
        }
    }
    render() {
        // ...
    }
}
```

当然， `fetchData` 是一个类的方法！(或者，更确切地说，一个类属性——但这不会改变任何东西。)由于状态的变化，它不会有所不同。所以 `this.props.fetchData` 将保持等于 `prevProps.fetchData` ，我们将永远不会重新获取。让我们删除这个条件呢？

``` js
componentDidUpdate(prevProps) {
    this.props.fetchData();
}
```

哦等等，这会在每次重新渲染时获取。(在上面的树中添加一个动画是发现它的一种有趣的方式。) 也许让我们将它绑定到特定的查询？

``` js
render() {
    return <Child fetchData = {
        this.fetchData.bind(this, this.state.query)
    }
    />;
}
```

但是，即使query没有改变， `this.props.fetchData！== prevProps.fetchData` 也始终 为 `true` ！所以我们总是会重新获取。
对于类的这个难题，惟一真正的解决方案是咬紧牙关，将 `query` 本身传递给 `Child` 组件。 `Child` 实际上并没有最终使用 `query` ，但它可以在更改时触发重新获取：

``` js
class Parent extends Component {
    state = {
        query: 'react'
    };
    fetchData = () => {
        const url = 'https://hn.algolia.com/api/v1/search?query=' + this.state.query;
        // ... Fetch data and do something ...
    };
    render() {
        return <Child fetchData = {
            this.fetchData
        }
        query = {
            this.state.query
        }
        />;
    }
}

class Child extends Component {
    state = {
        data: null
    };
    componentDidMount() {
        this.props.fetchData();
    }
    componentDidUpdate(prevProps) {
        if (this.props.query !== prevProps.query) {
            this.props.fetchData();
        }
    }
    render() {
        // ...
    }
}
```

在多年使用React类的过程中，我已经习惯了传递不必要的props并破坏父组件的封装，直到一周前我才意识到为什么必须这样做。
**对于类，函数props本身并不是数据流的真正组成部分。** 方法过于紧密于可变的 `this` 变量，这样我们就不能依赖它们的标识来表示任何内容。 因此，即使我们只想要一个函数，我们也必须传递一堆其他数据，以便能够“区分”它。我们无法知道从父级传递的 `this.props.fetchData` 是否依赖于某种状态，以及该状态是否刚刚更改。

使用 `useCallback` ，函数可以完全参与数据流。 我们可以说，如果函数输入发生了变化，函数本身就会发生变化，但如果没有变化，它就会保持不变。由于 `useCallback` 提供的粒度，对 `props.fetchData` 等 `props` 的更改可以自动向下传播。
同样， `useMemo` 让我们对复杂对象也这样做：

``` js
function ColorPicker() {
    // Doesn't break Child's shallow equality prop check
    // unless the color actually changes.
    const [color, setColor] = useState('pink');
    const style = useMemo(() => ({
        color
    }), [color]);
    return <Child style = {
        style
    }
    />;
}
```

**我想强调的是，在任何地方使用useCallback都非常笨重。** 当一个函数传递下去并从一些子组件的效果内部调用时它很有用。或者，如果你试图阻止破坏子组件的记忆。但Hooks更适合于避免回调完全传递下来。

在上面的例子中，我更希望 `fetchData` 位于我的 `effect` (它本身可以被提取到一个自定义的钩子中)或顶层导入中。我希望保持 `effect` 简单，并且其中的回调对此没有帮助。(“如果在请求运行期间某些 `props.onComplete` 回调发生了更改，该怎么办？”) 你可以模拟类行为，但这并不能解决竞争条件。

### 说说竞争条件

使用类的经典数据获取示例可能如下所示：

``` js
class Article extends Component {
    state = {
        article: null
    };
    componentDidMount() {
        this.fetchData(this.props.id);
    }
    async fetchData(id) {
        const article = await API.fetchArticle(id);
        this.setState({
            article
        });
    }
    // ...
}
```

你可能知道，这段代码有些问题。它不处理更新。所以你可以在网上找到的第二个经典例子是这样的：

``` js
class Article extends Component {
    state = {
        article: null
    };
    componentDidMount() {
        this.fetchData(this.props.id);
    }
    componentDidUpdate(prevProps) {
        if (prevProps.id !== this.props.id) {
            this.fetchData(this.props.id);
        }
    }
    async fetchData(id) {
        const article = await API.fetchArticle(id);
        this.setState({
            article
        });
    }
    // ...
}
```

这肯定更好！但他仍然有些问题。它有问题的原因是请求顺序可能出现问题。因此，如果我正在获取 `{id：10}` 的数据，切换到 `{id：20}` ，但 `{id：20}` 请求首先出现，先前开始但稍后完成的请求将错误地覆盖我的状态。

这被称为竞争条件，它在代码中是典型的，将 `async/await` （假定有东西在等待结果）与自顶向下的数据流（当我们处于异步函数的中间时，属性或状态可能会改变）混合在一起。

效果并不能神奇地解决这个问题，但是如果你试图将 `async` 函数直接传递给效果，它们会警告你。(我们需要改进这个警告，以便更好地解释你可能遇到的问题。)

如果你使用的异步方法支持取消，那就太棒了! 你可以在清理函数中取消异步请求。
**或者，最简单的权宜之计方法是使用布尔值跟踪它：**

``` js
function Article({
    id
}) {
    const [article, setArticle] = useState(null);

    useEffect(() => {
        +
        let didCancel = false;
        async function fetchData() {
            const article = await API.fetchArticle(id); +
            if (!didCancel) {
                setArticle(article);
            }
        }

        fetchData();

        // 这里也是你的关注点
        return () => {
            didCancel = true;
        };
    }, [id]);

    // ...
}
```

## 提高标准

* 使用类生命周期思维模式，副作用与渲染输出的行为不同。渲染UI由props和state驱动，并保证与它们一致，但副作用不是。这是bug的常见来源。

* 使用useEffect的思维模式，默认情况下会同步事物。副作用成为React数据流的一部分。对于每个useEffect调用，一旦你做对了，你的组件就会更好地处理边缘情况。

* 然而，做好这件事的前期成本更高。这可能很烦人。编写能够很好地处理边缘情况的同步代码，本质上比引发与渲染不一致的一次性副作用更困难。

* 如果useEffect是你大多数时间使用的工具，这可能会令人担忧。然而，它是一个低层的构建块。现在是使用钩子的早期阶段，所以每个人都一直在使用低级别的钩子，尤其是在教程中。但是在实践中，随着好的API获得发展势头，社区很可能会开始转向更高级别的钩子。

* 我看到不同的应用程序创建了它们自己的钩子，比如封装了它们的一些应用程序的auth逻辑的useFetch或使用主题上下文的useTheme。一旦你有了这些工具箱，你就不会经常使用useEffect。但它带来的弹性使每个Hook都能在其上构建。

* 到目前为止，useEffect最常用于数据获取。但是数据提取并不是一个同步问题。这一点尤其明显，因为我们的deps经常是[]。我们在同步什么?

* 从长远来看，数据获取的suspense将允许第三方库以一种一流的方式告诉React暂停渲染，直到异步(任何东西: 代码、数据、图像)就绪。

* 随着suspense逐渐覆盖更多的数据获取用例，我预计当你真正想要同步props和状态到某个副作用时，useEffect将作为一个高级用户工具淡出背景。与数据获取不同，它可以很自然地处理这种情况，因为它是为这种情况而设计的。但在此之前，这里所示的自定义钩子是重用数据获取逻辑的好方法。

## 笔者个人总结

> 其实这个就像前面所说的类和函数的区别了。因为在React中，props是不会改变的，而this(state)是可变的。所以造成了改变。由于props是不可变的，这样针对每次的渲染，都会有一个特定的渲染(每次的渲染)。

> 对于effect内使用的函数，最好是放在effect中，这样可以避免以后组件增加而未处理所有的情况。如果有复用，可以把这些函数提到effect外面，使用useCallback包裹起来。

作者：xiaohesong
链接：https://www.jianshu.com/p/ab432b6344f6
来源：简书
