---
description: 元素是构建React 应用的最小组成
---

# Rendering Elements

一个你想在屏幕上看见的元素描述：

```jsx
const element = <h1>Hello, world</h1>;
```

不像浏览器的DOM元素，React 元素是普通的对象，容易创建，React DOM 关心DOM更新以匹配React 元素。

{% hint style="warning" %}
注意

人们可能混淆（confuse）元素和更广泛（widely）的概念“组件“，我们将会在下一节介绍组件，组件是由元素构建的，我们鼓励（encourage）你去下一节之前阅读本节。
{% endhint %}

#### 向DOM中渲染一个元素

假设这有一个`div` 在你HTML文件的某个地方

```markup
<div id="root"></div>
```

我们把它称为一个“root“ DOM 节点，因为在它内部所有的事情都会被React DOM管理。

仅仅通过React构建的应用通常只有一个跟节点。如果你整合React 到一个存在的应用，你可以有许多单独（isolated）的节点。

在root DOM 节点中渲染React 元素，把这两个（：一个是DOM节点一个是React元素）传入`ReactDOM.render()`:

```jsx
const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));
```

[**Try it on CodePen**](https://reactjs.org/redirect-to-codepen/rendering-elements/render-an-element)

在页面中显示“Hello,world“

#### 更新被渲染的元素

React 元素是[immutable](https://en.wikipedia.org/wiki/Immutable_object).（不可变的），当你创建一个元素你就不能该拜年他的子节点或者属性。一个元素像是电影里的一帧：它代表（represents）在某些（certain）时间点的UI。

在我们以后的知识中，只有一种方式更新UI，那就是创建一个新的元素，然后传进`ReactDOM.render()`.

思考这个钟表例子：

```jsx
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, document.getElementById('root'));
}

setInterval(tick, 1000);
```

它调用`ReactDOM.render()` 每秒通过`setInterval()` 回调

{% hint style="warning" %}
**Node:**

在practice\(实践\)中，大多数React 应用只调用ReactDOM.render\(\)一次，下一节我们学习如何得到一个encapsulated（封装）到有状态的组件

我们建议你别跳节看，它们之间相互依赖
{% endhint %}

### React Only Updates What's Necessary

React DOM 比较元素和它的子节点当前状态和前一个状态，并只在DOM desired（需要的）状态下做必需的更新

你可以检查验证下面的例子通过浏览器工具

尽管我们创建了一个在每一秒描述整个UI树的元素，只有text节点内容通过React DOM 得到了更新的改变

我们的experience\(经验\)告诉我们，考虑到用户界面应该如何展示任何给定的时刻，而不是如何随着时间的推移而改变它，可以消除一类的bug



























