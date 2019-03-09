---
description: 这节介绍State和生命周期的概念，你可以看详细的API 参考
---

# State and Lifecycle

思考这个时刻表，在渲染元素，我们只学习一种方式去更细UI，就是调用`ReactDOM.render()` 去改变渲染输出：

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

在这节，我们讲学习如何制作这个Clock组件真正的封装并复用,它会建立自身的计时器并每秒更新自身，

我门从怎么封装这个clock外观开始：

```jsx
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

然而，他忽略一个关键的需求：`Clock` 设置定时器更新ui这一需求，应该是`Clock` 自身实现的一个细节

ideally（理想上）我们只想写一次这样的代码，然后让它自己更新

```jsx
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

在这个实例中，我们需要给Clock 添加 “state”

State 是一个小的props，但是它是私有的完全受组件控制。

我们mentioned（提到）在之前组件定义作为类具有一些附加特性。本地state恰巧就是这样的，只有这个类才能的到这个特性。

### Converting a Function to a Class

你可以通过五步转换一个想Clock的函数组件

1. 创建一个ES6类，同样的名字继承React.Component.
2. 添加一个`render()` 的空方法
3. 移动函数体到`render()` 方法里
4. 替换`props` 为 `this.props` 到`render()`内、
5. 删除剩下的函数声明

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

`Clock` 现在是作为一个类被定义而不是一个函数。

`render` 方法将会在每次更新发生的时候被调用，但是只要我们渲染`<Clock />` 在相一个节点，只要一个`Clock` 实力被使用，就会让我们使用其他属性比如本地状态和生命周期函数

### Adding local State to a Class

我们将通过三步从props中移动数据到state：

1. 在`render()` 方法里用`this.state.date` 替换 `this.props.date`:

```jsx
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

  2. 添加一个class constructir 赋值给初始化的`this.state`:

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

注意我们怎么在构造函数中传入`props`:

```jsx
 constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
```

Class 组件应该总是调用基本的构造函数

 3. 从`<Clock />`删除`date` 属性

```jsx
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

一会我们把定时器代码添加回组件本身

结果看起来应该是这样：

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

下一步，我们将制作`Clock` 的定时器并且每秒更新它自己。

### Adding Lifecycle Methods to a Class

应用有许多组件，很重要的一点就是在组件销毁的时候释放资源。

 我们想第一时间设置一个定时器无论`Clock` 在什么时候渲染到DOM。React 叫做“mounting”

我们还想在`Clock` 被删除的时候，在DOM产生使清除定时器，React 叫做 "ummounting"

我们在组件mounts 和unmounts 运行一些特殊定义的方法的代码。

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

这些函数叫做生命周期函数。

`componentDidMount()` 方法运行在组件已经被渲染到DOM之后。这是设置定时器的一个好地方。

```jsx
 componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

注意定时器ID我们是怎么保存的。

同时`this.props` 被React自身设置，`this.state` 有了特殊的含义。如果你需要存储一些不participate\(参于\)数据流的数据比如一个定时器ID，你可以吧它添加到额外的属性到类中。

我们拆毁定时器在`compionentWillUnmout()` 生命周期方法：

```jsx
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

最后，我们实现一个每秒运行在`Clcok` 组件中的方法方法叫做`tick()`

它将会使用`this.setState()` 通过组件的本地状态计划更新。

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

现在时钟每秒滴答作响。

我们快速概述一下发生了什么和方法调用的顺序。

1. 当`<Clock />`  被传入到`ReactDOM.render()` React调用Clock组件的构造函数，一旦Clock需要在当前展示，它就初始化this.state一个包含当前时间的对象.我们一会更新这个状态。
2. 然后React 调用 Clock 组件的render\(\) 方法，这就是React 了解需要在屏幕中展示什么，然后React 更新DOM 来匹配Clock的渲染输出。
3. 当Clock 输出插入到DOM，React 调用componentDIdMount\(\) 方法，在它的内部，Clock组件请求浏览器设置一个定时器每秒调用组件的tick（）方法
4. 每秒调用tick\(\) 方法的内部，Clock通过一个包含当前时间的对象调用setState\(\) 方法计划更新UI,感谢setState\(\) 的调用，React 知道了状态已经改变，并再一次调用render\(\) 方法去知道屏幕上应该展示什么，这时候，this.state.date 在render\(\) 方法里将会不同，所以渲染的输出将会包括



