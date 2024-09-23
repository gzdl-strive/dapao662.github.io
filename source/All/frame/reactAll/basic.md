---
title: React基本使用
date: 2021-12-10 10:08:24
toc: true
---

### 虚拟 DOM 的两种创建方式

```js
//1、jsx创建
const VDOM = (
  /* 此处一定不要写引号，因为不是字符串 */
  <h1 id="title">
    <span>Hello,React</span>
  </h1>
);

//2、js创建
const VDOM = React.createElement(
  "h1",
  { id: "title" },
  React.createElement("span", {}, "Hello,React")
);
```

**虚拟 DOM**
关于虚拟 DOM：

1. 本质是 Object 类型的对象（一般对象）
2. 虚拟 DOM 比较“轻”，真实 DOM 比较“重”，因为虚拟 DOM 是 React 内部在用，无需真实 DOM 上那么多的属性。
3. 虚拟 DOM 最终会被 React 转化为真实 DOM，呈现在页面上。

### JSX 语法规则

1.  定义虚拟 DOM 时，不要写引号。
2.  标签中混入 JS 表达式时要用`{}`。
3.  样式的类名指定不要用 class，要用 className。
4.  内联样式，要用`style={{key:value}}`的形式去写。
5.  只有一个根标签
6.  标签必须闭合
7.  标签首字母
    (1).若小写字母开头，则将该标签转为 html 中同名元素，若 html 中无该标签对应的同名元素，则报错。
    (2).若大写字母开头，react 就去渲染对应的组件，若组件没有定义，则报错。

**一定注意区分：【js 语句(代码)】与【js 表达式】**
(1). 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方
下面这些都是表达式：

```js
a;
a + b;
demo(1);
arr.map();
function test() {}
```

(2). 语句(代码)：
下面这些都是语句(代码)：

```js
  if(){}
  for(){}
  switch(){case:xxxx}
```

### 模块与组件、模块化与组件化的理解

#### 1、模块

1. 理解：向外提供特定功能的 js 程序, 一般就是一个 js 文件
2. 为什么要拆成模块：随着业务逻辑增加，代码越来越多且复杂。
3. 作用：复用 js, 简化 js 的编写, 提高 js 运行效率

#### 2、组件

1. 理解：用来实现局部功能效果的代码和资源的集合(html/css/js/image 等等)
2. 为什么要用组件： 一个界面的功能更复杂
3. 作用：复用编码, 简化项目编码, 提高运行效率

#### 3、模块化

当应用的 js 都以模块来编写的, 这个应用就是一个模块化的应用

#### 4、组件化

当应用是以多组件的方式实现, 这个应用就是一个组件化的应用

### ES6 类回顾

1. 类中的构造器不是必须要写的，要对实例进行一些初始化的操作，如添加指定属性时才写。
2. 如果 A 类继承了 B 类，且 A 类中写了构造器，那么 A 类构造器中的 super 是必须要调用的。
3. 类中所定义的方法，都放在了类的原型对象上，供实例去使用。

```js
//创建一个Person类
class Person {
  //构造器方法
  constructor(name, age) {
    //构造器中的this是谁？—— 类的实例对象
    this.name = name;
    this.age = age;
  }

  //类中可以直接写赋值语句,如下代码的含义是：给Person的实例对象添加一个属性，名为a，值为1
  //如果不用通过初始化时传参可以直接在类中写赋值语句
  a = 1;
  wheel = 4;
  static demo = 100;

  //一般方法
  speak() {
    //speak方法放在了哪里？——类的原型对象上，供实例使用
    //通过Person实例调用speak时，speak中的this就是Person实例
    console.log(`我叫${this.name}，我年龄是${this.age}`);
  }
}
```

### 函数式和类式组件

```js
//1.创建函数式组件
function MyComponent() {
  console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
  return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>;
}
//2.创建类式组件
class MyComponent extends React.Component {
  render() {
    //render是放在哪里的？—— MyComponent的原型对象上，供实例使用。
    //render中的this是谁？—— MyComponent的实例对象 <=> MyComponent组件实例对象。
    console.log("render中的this:", this);
    return <h2>我是用类定义的组件(适用于【复杂组件】的定义)</h2>;
  }
}
```

**执行了`ReactDOM.render(<MyComponent/>.......)`之后，发生了什么？**

1. React 解析组件标签，找到了 MyComponent 组件。
2. 发现组件是使用类定义的，随后 new 出来该类的实例，并通过该实例调用到原型上的 render 方法。
3. 将 render 返回的虚拟 DOM 转为真实 DOM，随后呈现在页面中。

### 类方法中的 this 指向

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  study() {
    //study方法放在了哪里？——类的原型对象上，供实例使用
    //通过Person调用study时，study中的this指向Person实例
    console.log(this);
  }
}

const p1 = new Person("tom", 18);
p1.study(); //通过实例调用，this指向实例

const x = p1.study; //这是一个赋值语句，没有调用study方法
x(); //这个时候直接调用，不是通过实例调用
//类中的方法默认是开启了局部的严格模式，所以this会指向undefined
```

> 类中的方法默认是开启了局部的严格模式，所以 this 会指向`undefined`

### 组件实例三大属性

#### 1、state

> - state 是组件对象最重要的属性, 值是对象(可以包含多个 key-value 的组合)
> - 组件被称为"状态机", 通过更新组件的 state 来更新对应的页面显示(重新渲染组件)

```js
class Weather extends React.Component {
  //构造器调用几次？ ———— 1次
  constructor(props) {
    console.log("constructor");
    super(props);
    //初始化状态
    this.state = { isHot: false, wind: "微风" };
    //解决changeWeather中this指向问题
    //构造器中的this指向的是实例
    //赋值语句
    // 实例上添加一个changeWeather方法   实例上没有changeWeather方法，向原型链上查找，找到原型对象上的changeWeather方法，bind方法会改变this指向，并返回一个函数，所以this.changeWeather.bind(this)就是一个this指向了实例的一个新函数
    this.changeWeather = this.changeWeather.bind(this);
  }

  //render调用几次？ ———— 1+n次 1是初始化的那次 n是状态更新的次数
  render() {
    console.log("render");
    //读取状态
    const { isHot, wind } = this.state;
    return (
      <h1 onClick={this.changeWeather}>
        今天天气很{isHot ? "炎热" : "凉爽"}，{wind}
      </h1>
    );
  }

  //changeWeather调用几次？ ———— 点几次调几次
  changeWeather() {
    //changeWeather放在哪里？ ———— Weather的原型对象上，供实例使用
    //由于changeWeather是作为onClick的回调，所以不是通过实例调用的，是直接调用
    //类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined

    console.log("changeWeather");
    //获取原来的isHot值
    const isHot = this.state.isHot;
    //严重注意：状态必须通过setState进行更新,且更新是一种合并，不是替换。
    this.setState({ isHot: !isHot });
    console.log(this);

    //严重注意：状态(state)不可直接更改，下面这行就是直接更改！！！
    //this.state.isHot = !isHot //这是错误的写法
  }
}
```

**类组件简写形式**

```js
class Weather extends React.Component {
  //初始化状态->可以不用使用构造器，这样定义会直接添加到实例上
  state = { isHot: false, wind: "微风" };
  render() {
    const { isHot, wind } = this.state;
    return (
      <h1 onClick={this.changeWeather}>
        今天天气很{isHot ? "炎热" : "凉爽"}，{wind}
      </h1>
    );
  }
  //自定义方法————要用赋值语句的形式+箭头函数
  changeWeather = () => {
    const isHot = this.state.isHot;
    this.setState({ isHot: !isHot });
  };
}
```

#### 2、props

**props 基本使用**

```js
class Person extends React.Component {
  render() {
    const { name, age, sex } = this.props;
  }
}
ReactDOM.render(<Person name="s1mple" age={18} sex={"男"} />);
```

**对 props 进行限制**

```js
//引入prop-types 用于对组件标签属性进行限制
import PropTypes from 'xxx';
funciton Person() {}
Person.propTypes = {
  name: PropTypes.string.isRequired,//限制name必传，且为字符串
  //...
}
//指定默认标签属性值
Person.defaultProps = {
  sex:'男',//sex默认值为男
  age:18 //age默认值为18
}
```

**props 的简写**

```js
class Person extends React.Component {
  constructor(props) {
    //构造器是否接收props，是否传递给super，取决于：是否希望在构造器中通过this访问props
    // console.log(props);
    super(props);
    console.log("constructor", this.props);
  }
  //对标签属性进行类型、必要性的限制
  static propTypes = {
    name: PropTypes.string.isRequired, //限制name必传，且为字符串
    sex: PropTypes.string, //限制sex为字符串
    age: PropTypes.number, //限制age为数值
  };
  //指定默认标签属性值
  static defaultProps = {
    sex: "男", //sex默认值为男
    age: 18, //age默认值为18
  };
  render() {
    const { name, age, sex } = this.props;
    //props是只读的
    //this.props.name = 'jack' //此行代码会报错，因为props是只读的
    return null;
  }
}
```

**扩展属性: 将对象的所有属性通过 props 传递**

```js
/*
在js中，对象不是可迭代类型的数据，不能使用...person展开
以下代码中{}代表里面的是js表达式，为什么可以展开呢，因为React内部处理了...person，把它变成键值对的形式加到属性上
*/
<Person {...person} />
```

#### 3、refs

**字符串形式的 ref**

```js
class Demo extends React.Component {
  //展示左侧输入框的数据
  showData = () => {
    const { input1 } = this.refs;
    alert(input1.value);
  };
  render() {
    return (
      <input
        ref="input1"
        type="text"
        onBlur={this.showData}
        placeholder="点击按钮提示数据"
      />
    );
  }
}
```

**回调函数形式的 ref**

```js
class Demo extends React.Component {
  showData = () => {
    const { input1 } = this;
    alert(input1.value);
  };
  render() {
    return (
      <input
        ref={(c) => (this.input1 = c)}
        type="text"
        onBlur={this.showData}
        placeholder="点击按钮提示数据"
      />
    );
  }
}
```

**回调 ref 回调指向次数问题**
关于回调 refs 的说明
如果 ref 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 null，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的

```js
class Demo extends React.Component {
  state = { isHot: false };

  showInfo = () => {
    const { input1 } = this;
    alert(input1.value);
  };

  changeWeather = () => {
    //获取原来的状态
    const { isHot } = this.state;
    //更新状态
    this.setState({ isHot: !isHot });
  };

  saveInput = (c) => {
    this.input1 = c;
    console.log("@", c);
  };

  render() {
    const { isHot } = this.state;
    return (
      <div>
        <input ref={this.saveInput} type="text" />
        <button onClick={this.showInfo}>点我提示输入的数据</button>
        <button onClick={this.changeWeather}>点我切换天气</button>
      </div>
    );
  }
}
```

**createRef 的使用**
`React.createRef`调用后可以返回一个容器，该容器可以存储被 ref 所标识的节点,该容器是“专人专用”的

```js
class Demo extends React.Component {
  myRef = React.createRef();
  myRef2 = React.createRef();
  showData = () => {
    alert(this.myRef.current.value);
  };
  render() {
    return <input ref={this.myRef} type="text" onBlur={this.showData} />;
  }
}
```

### 事件处理

(1).通过 onXxx 属性指定事件处理函数(注意大小写)
a.React 使用的是自定义(合成)事件, 而不是使用的原生 DOM 事件 —————— 为了更好的兼容性
b.React 中的事件是通过事件委托方式处理的(委托给组件最外层的元素) ————————为了的高效
(2).通过 event.target 得到发生事件的 DOM 元素对象 ——————————不要过度使用 ref（发生事件的元素正好是 ref 绑定的元素，可以使用 event）

### 收集表单数据

#### 1、非受控组件

```js
class Login extends React.Component {
  handleSubmit = (event) => {
    event.preventDefault(); //阻止表单提交
    const { username, password } = this;
    alert(
      `你输入的用户名是：${username.value},你输入的密码是：${password.value}`
    );
  };
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        用户名：
        <input ref={(c) => (this.username = c)} type="text" name="username" />
        密码：
        <input
          ref={(c) => (this.password = c)}
          type="password"
          name="password"
        />
        <button>登录</button>
      </form>
    );
  }
}
```

#### 2、受控组件

```js
//创建组件
class Login extends React.Component {
  //初始化状态
  state = {
    username: "", //用户名
    password: "", //密码
  };

  //保存用户名到状态中
  saveUsername = (event) => {
    this.setState({ username: event.target.value });
  };

  //保存密码到状态中
  savePassword = (event) => {
    this.setState({ password: event.target.value });
  };

  //表单提交的回调
  handleSubmit = (event) => {
    event.preventDefault(); //阻止表单提交
    const { username, password } = this.state;
    alert(`你输入的用户名是：${username},你输入的密码是：${password}`);
  };

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        用户名：
        <input onChange={this.saveUsername} type="text" name="username" />
        密码：
        <input onChange={this.savePassword} type="password" name="password" />
        <button>登录</button>
      </form>
    );
  }
}
```

### 生命周期

#### 旧生命周期

1. 初始化阶段: 由 ReactDOM.render()触发---初次渲染
1. constructor()
1. componentWillMount()
1. render()
1. componentDidMount() =====> 常用
   一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
1. 更新阶段: 由组件内部 this.setSate()或父组件 render 触发
1. shouldComponentUpdate()
1. componentWillUpdate()
1. render() =====> 必须使用的一个
1. componentDidUpdate()
   强制更新：forceUpdate() -> 不更改任何状态中的数据，强制更新一下 -》 不受阀门控制
1. 卸载组件: 由 ReactDOM.unmountComponentAtNode()触发
1. componentWillUnmount() =====> 常用
   一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息

![旧生命周期图](/assets/reactImg/reactAllImg/lifeCircle_old.png "旧生命周期")

```js
//...
//强制更新按钮的回调
force = ()=>{
  //不更改任何状态中的数据，强制更新一下
  this.forceUpdate()
}

//控制组件更新的“阀门”
shouldComponentUpdate(){
  console.log('Count---shouldComponentUpdate');
  return true
}
//...
```

#### 新生命周期

1. 初始化阶段: 由 ReactDOM.render()触发---初次渲染
1. constructor()
1. getDerivedStateFromProps
1. render()
1. componentDidMount() =====> 常用
   一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
1. 更新阶段: 由组件内部 this.setSate()或父组件重新 render 触发
1. getDerivedStateFromProps
1. shouldComponentUpdate()
1. render()
1. getSnapshotBeforeUpdate
1. componentDidUpdate()
1. 卸载组件: 由 ReactDOM.unmountComponentAtNode()触发
1. componentWillUnmount() =====> 常用
   一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息

![新生命周期图](/assets/reactImg/reactAllImg/lifeCircle_new.png "新生命周期图")

```js
//...
//若state的值在任何时候都取决于props，那么可以使用getDerivedStateFromProps
static getDerivedStateFromProps(props,state){
  console.log('getDerivedStateFromProps',props,state);
  return null
}

//在更新之前获取快照
getSnapshotBeforeUpdate(){
  console.log('getSnapshotBeforeUpdate');
  return 'atguigu'
}
//...
```

### DOM 的 diffing 算法

```js
/*
经典面试题:
  1). react/vue中的key有什么作用？（key的内部原理是什么？）
  2). 为什么遍历列表时，key最好不要用index?
  
  1. 虚拟DOM中key的作用：
      1). 简单的说: key是虚拟DOM对象的标识, 在更新显示时key起着极其重要的作用。

      2). 详细的说: 当状态中的数据发生变化时，react会根据【新数据】生成【新的虚拟DOM】, 
                    随后React进行【新虚拟DOM】与【旧虚拟DOM】的diff比较，比较规则如下：

              a. 旧虚拟DOM中找到了与新虚拟DOM相同的key：
                    (1).若虚拟DOM中内容没变, 直接使用之前的真实DOM
                    (2).若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM

              b. 旧虚拟DOM中未找到与新虚拟DOM相同的key
                    根据数据创建新的真实DOM，随后渲染到到页面
              
  2. 用index作为key可能会引发的问题：
            1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作:
                    会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低。

            2. 如果结构中还包含输入类的DOM：
                    会产生错误DOM更新 ==> 界面有问题。
                    
            3. 注意！如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，
              仅用于渲染列表用于展示，使用index作为key是没有问题的。
      
  3. 开发中如何选择key?:
            1.最好使用每条数据的唯一标识作为key, 比如id、手机号、身份证号、学号等唯一值。
            2.如果确定只是简单的展示数据，用index也是可以的。
*/

/* 
慢动作回放----使用index索引值作为key

  初始数据：
      {id:1,name:'小张',age:18},
      {id:2,name:'小李',age:19},
  初始的虚拟DOM：
      <li key=0>小张---18<input type="text"/></li>
      <li key=1>小李---19<input type="text"/></li>

  更新后的数据：
      {id:3,name:'小王',age:20},
      {id:1,name:'小张',age:18},
      {id:2,name:'小李',age:19},
  更新数据后的虚拟DOM：
      <li key=0>小王---20<input type="text"/></li>
      <li key=1>小张---18<input type="text"/></li>
      <li key=2>小李---19<input type="text"/></li>

-----------------------------------------------------------------

慢动作回放----使用id唯一标识作为key

  初始数据：
      {id:1,name:'小张',age:18},
      {id:2,name:'小李',age:19},
  初始的虚拟DOM：
      <li key=1>小张---18<input type="text"/></li>
      <li key=2>小李---19<input type="text"/></li>

  更新后的数据：
      {id:3,name:'小王',age:20},
      {id:1,name:'小张',age:18},
      {id:2,name:'小李',age:19},
  更新数据后的虚拟DOM：
      <li key=3>小王---20<input type="text"/></li>
      <li key=1>小张---18<input type="text"/></li>
      <li key=2>小李---19<input type="text"/></li>
*/
```
