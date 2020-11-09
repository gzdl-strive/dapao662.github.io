---
title: react技巧一
date: 2020-11-09 21:19:10
tag: react
---

## 取消请求
>React中当前正在发出请求的组件从页面上卸载了，理想情况下这个请求也应该取消掉，那么如何把请求的取消和页面的卸载关联在一起呢？
**这里要考虑利用 useEffect 传入函数的返回值：**
```js
useEffect(() => {
    return () => {
        //页面卸载时执行
    }
})
```
**假设我们的请求是利用fetch,那么还有一个需要运用的知识点：`AbortController`,简单看一下它的用法:**
```js
const abortController = new AbortController();
fetch(url, {
    //这里传入signal进行关联
    signal: abortController.signal,
});

//这里调用abort即可取消请求
abortController.abort();
```
**那么结合 React 封装一个 useFetch 的 hook：**
```js
export function useFetch = (config, deps) => {
    const abortController = new AbortController();
    const [loading, setLoading] = useState(false);
    const [result, setResult] = useState();

    useEffect(() => {
        setLoading(true);
        fetch({
            ...config,
            signal: abortController.signal
        })
            .then(res => setResult(res))
            .finally(() => setLoading(false))
    }, deps)

    useEffect(() => {
        return () => abortController.abort()
    }, [])

    return { result, loading }

}
```
**那么比如在路由发生切换，Tab 发生切换等场景下，被卸载掉的组件发出的请求也会被中断。**

## 深比较依赖
>在使用 `useEffect` 等需要传入依赖的 `hook` 时，最理想的状况是所有依赖都在真正发生变化的时候才去改变自身的引用地址，但是有些依赖不太听话，每次渲染都会重新生成一个引用，但是内部的值却没变，这可能会让 useEffect 对于依赖的「浅比较」没法正常工作。

比如说：
```js
const getDep = () => {
    return {
        foo: 'bar',
    };
};
useEffect(() => {
    //无限循环了
}, [getDep()]);
```
**这是一个人为的例子，由于`getDeps`函数返回的对象每次执行都是一个全新的引用，所以会导致触发渲染->`effect`->渲染->`effect`的无限更新。**

**有一个比较取巧的解决办法，把依赖转为字符串：**
```js
const getDep = () => {
    return {
        foo: 'bar',
    };
};
const dep = JSON.stringify(getDep());
useEffect(() => {
    //ok!
}, [dep])
```
这样对比的就是字符串` "{ foo: 'bar' }" `的值，而不是对象的引用，那么只有在值真正发生变化时才会触发更新。
当然最好还是用社区提供的方案：`useDeepCompareEffect`，它选用深比较策略，对于对象依赖来说，它逐个对比 `key` 和 `value`，在性能上会有所牺牲。
如果你的某个依赖触发了多次无意义的接口请求，那么宁愿选用 `useDeepCompareEffect` ，在对象比较上多花费些时间可比重复请求接口要好得多。
`useDeepCompareEffect `大致原理：
```js
import { isEqual } from 'lodash';
export function useDeepCompareEffect(fn, deps) {
    const trigger = useRef(0);
    const preDeps = useRef(deps);
    if(!isEqual(preDeps.current, deps)) {
        trigger.current++;
    }
    preDeps.current = deps;
    return useEffect(fn, [trigger.current]);
}
```
真正传入 `useEffect` 用以更新的是 `trigger` 这个数字值。用`useRef` 保留上一次传入的依赖，每次都利用 `lodash` 的 `isEqual` 对本次依赖和旧依赖进行深比较，如果发生变化，则让 `trigger` 的值增加。
当然我们也可以用` fast-deep-equal` 这个库，根据官方的 `benchmark` 对比，它比` lodash `的效率高 7 倍左右。

## 以URL为数据仓库
在公司内部的后台管理项目中，无论你做的系统面向的人群是运营还是开发，都会涉及到分享，那么保留「页面状态」就非常重要了。比如我是运营 A，在使用一个内部数据平台，我一定是想向运营 B 分享某 App 的消费数据的第二页，并且筛选为某个用户的状态的网页，并且进行讨论。那么状态和 URL 同步就尤为重要了。
>在传统的状态管理思路中，我们需要在代码里用`redux`、`recoil`等库去做一系列的数据管理，但是如果把 URL 后面的那串 query 想象成数据仓库呢？是不是也可以，尝试配合`react-router`封装一下。
```js
export function useQuery() {
    const histroy = useHistory();
    const { search, pathname } = useLocation();
    //保存query状态
    const queryState = useRef(qs.parse(search));
    //设置query
    const setQuery = handler => {
        const nextQuery = handle(queryState.current);
        queryState.current = nextQuery;
        //replace会使组件重新渲染
        history.replace({
            pathname: pathname,
            search: qs.stringify(nextQuery),
        });
    };
    return [queryState.current, setQuery];
}
```
在组件中，可以这样使用：
```js
const [query, setQuery] = useQuery();

// 接口请求依赖 page 和 size
useEffect(() => {
  api.getUsers();
}, [query.page, query, size]);

// 分页改变 触发接口重新请求
const onPageChange = page => {
  setQuery(prevQuery => ({
    ...prevQuery,
    page,
  }));
};

```
这样，所有的页面状态更改都会自动同步到 URL，非常方便。





作者：ssh_晨曦时梦见兮
链接：https://juejin.im/post/6885957893724569613
来源：掘金