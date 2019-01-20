---
description: 介绍 JSX
---

# Introducing JSX

想一下这个变量声明

```jsx
const element = <h1>Hello, world!</h1>;
```

这个有趣的语法标签既不是string也不是HTML

这种语法拓展叫做JSX，我们建议用这种语法去描述（describe）UI应该是什么样的，JSX可能让你认为是一种模版语言，但他提供了关于JavaScript的所有功能

JSX 生产Resct 元素，我们将会探索（explore）如何在DOM中渲染他们。下面我们来学习使用JSX的基本知识。

#### 什么是JSX

React 包含这一个事实，渲染逻辑和其他UI逻辑耦合在一起:事件如何被处理，状态随着时间推移怎么变化，和数据如何按照预计的展示

取代通过标记手动分隔逻辑在不同的文件，React 分隔思想和松散的耦合单元被叫做组件。我们在未来会重新了解components，但是你把标记放在js中感觉不舒适，看看这篇文章会说服你。

React 不要求使用JSX 语法，但是许多人发现把它作为视觉辅助嵌入JavaScript代码中的很有帮助，它允许React 去展示更多关于error和warning有用的信息。

whit that out of the way（别挡道了，注：感觉怪怪的） ，我们开始吧！

#### 在JSX中嵌入表达式

在下面的例子中，我们声明了一个变量叫做`name` 然后用{}包裹它放到JSX中使用

```jsx
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

你可以把一些有效的JavaScript表达式用花括号嵌入到JSX，比如`2+2` ,`user.firstName`,或者 `formatName(user)` 都是有效的表达式。

在下面的例子中，我们嵌入运行JavaScript函数的结果，`formatName(user)`,到一个`<h1>` 元素中。



```jsx
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

[**Try it on CodePen**](https://reactjs.org/redirect-to-codepen/introducing-jsx)

我们为了可读性把JSX分成多行，这不是必须的，当这么做的时候，我们建议用圆括号包起来避免（avoid）自动插入分号的坑（pitfalls）

#### JSX 也是一个表达式

编译（compilation）后，JSX表达式成为常规的（regular）JavaScript函数调用，对象的求值（evaluate）

这意味这你可以使用JSX 嵌入到`if` 声明和`for` 循环，将其赋给变量，作为参数，通过函数返回：

```jsx
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

#### JSX指定属性

你可以使用引号指定一个字符串字面量（iterals）作为属性：

```jsx
const element = <div tabIndex="0"></div>;
```

你也可以通过花括号（curly braces）嵌入一个JavaScript表达式到一个属性中：

```jsx
const element = <img src={user.avatarUrl}></img>;
```

别再花括号周围加引号，你应该使用在放字符串的时候使用引号，或者在放表达式的时候用花括号，但是这两种方式不要在同一个属性使用。

{% hint style="warning" %}
警告

因为JSX比HTML更接近JavaScript，React DOM 使用 驼峰命名规范（convention）代替HTML 属性名

比如，`class` 变成 className ，tabindex 变成 tabIndex。 
{% endhint %}

#### JSX指定子节点

如果标签是空的你可以立即（immediately）通过`/>`关闭它,就像XML一样

```jsx
const element = <img src={user.avatarUrl} />;
```

JSX 标签可以包含子节点

```jsx
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

#### JSX 防止（prevents）注入（injection）攻击（attacks）

在JSX中嵌入（embed）用户输入是安全的：

```jsx
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
const element = (  <h1 className="greeting">    Hello, world!  </h1>);
const element = React.createElement(  'h1',  {className: 'greeting'},  'Hello, world!');

```

#### JSX 对象的表现

babel把JSX编译成`React.createElement()` 的调用

这两个例子是一样的：

```jsx
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```jsx
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

`React,createElement()` 执行一些检测帮助你写没bug的代码，实际上（essentially）它创建了一个这样的对象：

```text
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

这些对象被叫做“React elements“，你可以认为他们是你想要在屏幕上看见的描述，React 读取这些对象然后使用他们构造DOM并且实时更新。

我们将会套索React elements 在DOM 如何渲染在下一章。

{% hint style="warning" %}
提示

我们建议使用 “Babel“语言定义[“Babel” language ](http://babeljs.io/docs/editors) 给你的编辑器，它为ES6和JSX代码提供高亮，这个网站使用Oceanic Next 配色方案兼容它
{% endhint %}





