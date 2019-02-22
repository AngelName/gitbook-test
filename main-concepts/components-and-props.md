# Components and Props



## Componts and Props

Components 拆分独立的UI，复用组件，专注思考一件事，下面文章介绍了组件思考方式，你可以找到[更多详细关于组件api](https://reactjs.org/docs/react-component.html)

概念上，组件就像JavaScript function，接受任意输入（叫 `props`） 并返回一个React 元素描述屏幕中应该有的样子

### Function and Class Components

通过一种简单的方式通过JavaScript function定义一个组件

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

这个函数是一个valid（有效的） React 组件，因为它接受一个 `props` （代表它的properties）对象数据参数返回一个React 元素，我们叫这样的组件为“function components” 因为它实际上（literally）JavaScript函数。

你可以使用ES6 class 去定义一个组件

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

上面两个组件在React的观点看来是一样的

Classes 有一些额外的功能，我们会在下一节介绍，从现在开始，为了简洁我们将使用函数组件

### Rendering a Component

Previously（以前），我们只是encountered（遇见） React 元素represent\(表示\)DOM标签

```javascript
const element = <div />;
```

然而，元素也可以represent\(表示\)用户定义的组件

```javascript
const element = <Welcome name="Sara" />;
```

当React 看见一个元素表示用户定义的组件，它通过JSX属性作为单个对象传给这个组件，我们叫这个对象`props`

例子：页面上渲染Hello Sara

```jsx
function Welcome(props) {
    return <h1 > Hello, {
        props.name
    } < /h1>;
}

const element = < Welcome name = "Sara" / > ;
ReactDOM.render(
    element,
    document.getElementById('root')
);
```

recap\(概括）这个例子发生了什么：

1.  我们调用`ReactDOM.render()`传入`<Welcome name=“Sara” />` 
2. 元素   React 调用 `Welcome` 组件传入`{name: 'Sara}` 作为 props
3. 我们的 `Welcome` 组件返回一个 `<h1>Hello,Sara</h1>`元素作为结果 
4. React DOM efficiently\(高效的）更新DOM 以匹配`<h1>Hello, Sara<.h1>`

{% hint style="warning" %}
注意: 组件的首字母大写 React Treats（对待） 组件开始通过小写字母作为DOM 标签，比如： Represents\(表示\) 一个HTML div tag，但是 表示一个组件requires `Welcome` 到作用域内 想了解这个约定背后的理由，请阅读[JSX In Depth](https://reactjs.org/docs/jsx-in-depth.html#user-defined-components-must-be-capitalized).
{% endhint %}

### Composing Components

组件可以引用其他组件到输出中，这点让我门可以使用相同的组件抽象一些细节层次，button form dialog\(对话\) screen:在React 应用，那些通常都能通过组件去表示

比如，我们创建一个App 组件去render  `Wlecome`   多次



```jsx
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcome name="Sara" />
      <Welcome name="Cahal" />
      <Welcome name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

Typically\(通常），一个新的React app 只有一个`App` 组件在顶级，然而，如果你想吧React 引入到一个已经存在的应用中，你可以自下而上通过像`Button`  这样的小组件\(gradually（逐渐）工作到视图hierarchy（层）的顶部

### Extraccting Components

别害怕拆分组件到更小的组件

比如，思考`Comment` 组件



```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

它接受author 一个对象，text string，date date作为 props，展示一个评论在一个社交媒体网站

这个组件很难更改由于整个嵌套，他也很难被复用，让我们提取一下组件出来

首先，我们提取`Avatar`

```jsx
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />
  );
}
```

`Avatar` 不需要知道它被`Comment` 渲染。这就是问什么我们需要给它提供prop 更加一般的名字，所以是`user` 而不是`author` 

建议从组件自己的角度去命名prors 而不是它被使用的上下文

简化一点的`Comment` 



```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

下一步，我们提取`UserInfo` 组件渲染`Avatar` 旁边的user's name:



```jsx
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
```

这让我们更近一步简化`Comment`

```jsx
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

提取组件在开始看起来很啰嗦，但是有一个palette（调色板\)可复用组件是值得的，一个好的规则是如果你的UI被使用several（多）次，或者是一个本身足够复杂，这种情况下都是可复用组件的候选。

Props are Read-Only

无论你declare（声明）一个组件用 function 还是class，它绝不能修改自己的props，想象这个`sum` 函数

```jsx
function sum(a, b) {
  return a + b;
}
```

像这样的函数叫纯函数，因为他们不attempt（尝试）改变输入，对于相同的输入总是返回相同的结果。

通过对比（contrast），这个函数是不纯的因为它改变了本身的输入

```jsx
function withdraw(account, amount) {
  account.total -= amount;
}
```

React 非常灵活但是有一个严格的规则：

#### 所有组件行为必须和纯函数一样作用于它们props

当然，应用UI 是动态的并时时改变的，在下一节，我们将会介绍新的概念“state”，State允许React组件改变他们的实时输出，响应用户行动，网络响应，和其他，不违反这个规则的

