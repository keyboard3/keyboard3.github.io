---
title: react-docs
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-31 11:10:32
password:
summary:
tags: [react, 翻译]
categories:
---

[React-Docs](https://reactjs.org/docs/getting-started.html)

# 开始

# 主要概念

# 高级指南

## Accessibility

## 代码分割

## Context

## Error Boundaries

## Forwarding Refs

Ref 转发是自动通过组件传递 ref 给它的子组件的一个技巧。它在应用中大多数组件是不需要使用的。但是，对于某些组件是需要的，尤其是重复使用的组件库。常见的场景描述如下。

### 转发 ref 给 DOM 组件

来看`FancyButton`组件，它渲染原生`button`DOM 元素：

```
function FancyButton(props) {
  return (
    <button className="FancyButton">
      {props.children}
    </button>
  );
}
```

React 组件隐藏它们的实现，包括它们自己的渲染结果。其他组件使用`FancyButton`通常不需要获取 ref 来引用内部的`button`DOM 元素。这很好，因为它会阻止组件过度依赖它们的 DOM 结构。
尽管`FeedStory`或`Comment`这种封装对于应用级组件比较理想，但像`FancyButton`或`MyTextInput`这类高度复用的`叶子`组件不方便。这些组件倾向于在应用中像`button`和`input`这类常规 DOM 一样使用，访问这些 DOM 节点可能无法避免去管理焦点，选中或者动画。
**Ref 转发是一个可选特性，让组件可以获取 ref，并向下传递给子组件**
在下面的例子中，`FancyButton`使用`React.forwardRef`去获取`ref`传递它，然后将它传递给渲染的 DOM `button`。

```
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你现在可以直接从DOM button上获取ref
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

这样，组件使用`FancyButton`可以获取底层`button`DOM 节点，如果需要可以访问它-就像你直接使用 DOM 节点一样。
这里一步一步解释了上面的例子发生了什么：

- 我们通过调用`React.createRef`创建了[React ref](https://reactjs.org/docs/refs-and-the-dom.html)，并给它赋值了变量
- 我们通过在 JSX 属性中指定它，向下传递 ref 到了`<FancyButton ref={ref}>`
- React 传递`ref`给`forwardRef`内的`(props,ref)=>...`函数，将它作为第二个参数
- 我们通过指定 JSX 属性将这个`ref`参数传递给`<button ref={ref}>`
- 当 ref 被附加, `ref.current`将指向`<button>`DOM 节点。
  > 注意
  > 第二个参数`ref`只有在当你用`React.frowardRef`定义组件时才存在。常规的函数和类组件不会接受`ref`参数，并且 ref 也不会存在到 Props 中。
  > Ref 转发不仅限于 DOM 组件。你也可以转发给 Class 实例。

---

### 组件库维护者注意

**当你在组件库中使用 ref 转发时，你议案将它作为一个大版本修改**。这是因为你的库可能会观察到不同的行为（取决于你 ref 赋值给谁，它是什么类型），它会导致应用崩溃因为其他库都依赖旧的行为。
尽管`React.forwardRef`存在是允许有条件的使用，但也不推荐：它会改变你库的行为并且会造成他们升级 React 时，用户的应用被破坏。

---

### 在高级组件中转发 ref

这个技巧对高级组件尤其有用（也叫做 HOC）。开始了解 logs 高阶组件，用于打印日志

```
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}
```

"logProps"高级组件将所有`props`传递给包裹的组件，所以渲染结果将会一致。比如，我们使用这个高阶组件去记录所有传递给`fancy button`组件的属性

```
class FancyButton extends React.Component {
  focus() {
    // ...
  }

  // ...
}

// 我们导出的是LogProps，而不是FancyButton
// 虽然它渲染的是FancyButton
export default logProps(FancyButton);
```

上面的例子有一个注意点：refs 将不会被传递。因为`ref`不是属性。就像`key`，它被 React 特殊处理。
如果你添加 ref 到高阶组件，这个 ref 指向的是最外面的容器组件，而不是里面的包装组件。
这意味着本来想指向`FancyButton`组件却实际上挂到了`LogProps`组件上

```
import FancyButton from './FancyButton';

const ref = React.createRef();

// 我们导入的FancyButton其实是LogProps高阶组件
// 它渲染的结果是一样的
// 我们的ref指向LogProps而不是内部的FancyButton
// 这意味着我们无法调用这类方法ref.current.focus()
<FancyButton
  label="Click Me"
  handleClick={handleClick}
  ref={ref}
/>;
```

幸运的是，我们可以通过`React.forwardRef`API 显示转发 ref 到内部的`FancyButton`组件上。`React.forwardRef`接受一个接收`props`和`ref`参数的渲染函数，并且返回 React 节点。例如：

```
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props);
    }

    render() {
      const {forwardedRef, ...rest} = this.props;

      // Assign the custom prop "forwardedRef" as a ref
      return <Component ref={forwardedRef} {...rest} />;
    }
  }

  // 注意第二个参数ref由React.forwardRef提供
  // 我们将ref作为正常属性传递给LogProps, e.g. "forwardedRef"
  // 然后它可以被附加到组件上
  return React.forwardRef((props, ref) => {
    return <LogProps {...props} forwardedRef={ref} />;
  });
}
```

### 在开发者工具中显示自定义名字

`React.forwardRef`接收一个渲染函数。 React 开发者工具用这个函数决定为转发组件显示的内容。
比如，下面的组件在开发者恐惧中将会显示"ForwardRef"

```
const WrappedComponent = React.forwardRef((props, ref) => {
  return <LogProps {...props} forwardedRef={ref} />;
});
```

如果你命名了这个渲染函数，开发者工具将显示将包括这个名字（比如：”ForwardRef(myFunction)“）

```
const WrappedComponent = React.forwardRef(
  function myFunction(props, ref) {
    return <LogProps {...props} forwardedRef={ref} />;
  }
);
```

你可以设置函数的`displayName`属性包含这个组件的显示

```
function logProps(Component) {
  class LogProps extends React.Component {
    // ...
  }

  function forwardRef(props, ref) {
    return <LogProps {...props} forwardedRef={ref} />;
  }

  // 在开发者工具显示这个组件的名字非常有帮助
  // e.g. "ForwardRef(logProps(MyComponent))"
  const name = Component.displayName || Component.name;
  forwardRef.displayName = `logProps(${name})`;

  return React.forwardRef(forwardRef);
}
```

## Fragments

## Higher-Order Components

## 集成其他库

## JSX in Depth

## 优化性能

## Portals

## Profiler

## 没有 ES6 的 React

## 没有 JSX 的 React

## Reconciliation

## Render Props

## 静态类型检查

## 严格模式

严格模式是为了突出应用中潜在的问题。像 Fragment 一样，严格模式不会渲染任何 UI。它会对后代元素进行额外的检查和警告。

> 注意：
> 严格模式检查只运行在开发模式下；它不会影响到生产环境构建
> 你可以在你的应用任何地方开启严格模式。比如

```
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```

在上面的例子中，严格模式检查不会影响到`Header`和`Footer`组件。但是，`ComponentOne`和`ComponentTwo`，作为它的后代将会被检查。
严格模式有下面这些用处：
[识别不安全生命周期的组件](https://reactjs.org/docs/strict-mode.html#identifying-unsafe-lifecycles)
[警告过使用时的字符串 refAPI](https://reactjs.org/docs/strict-mode.html#warning-about-legacy-string-ref-api-usage)
[警告使用废弃的 findDOMNode](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)
[检测不安全的 side effects](https://reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)
[检查过时的 Context API](https://reactjs.org/docs/strict-mode.html#detecting-legacy-context-api)
React 未来会添加更多的功能来支持

### 识别不安全生命周期

### 警告过使用时的字符串 refAPI

### 警告使用废弃的 findDOMNode

React 支持使用 findDOMNode 给定类的实例去 DOM 树种找。正常情况下你不需要，因为你可以[直接向 DOM 节点附加 ref](https://reactjs.org/docs/refs-and-the-dom.html#creating-refs)
`findDOMNode`仍然可以在类组件中使用，但是它会破坏抽象，因为允许父组件能单独访问指定的已经渲染的子组件。它会造成重构困难，你不能改变组件的实现因为父组件可以访问到 DOM 节点。`findDOMNode`当 Fragment 包含多个子元素时，会只返回第一个非空节点。`findDOMNode`是一次阅读 API。当你访问时，它才会给你结果。如果子组件渲染了不同的节点，它无法识别这个变更。因此`findDOMNode`仅对单个不可变的组件上有效。
另外你可以显式的将 ref 传递给你自定义的组件，并使用[ref 转发](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)传递给 DOM 节点上
你还可以给你的组件中包一个 DOM 节点，并直接附加上 ref

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.wrapper = React.createRef();
  }
  render() {
    return <div ref={this.wrapper}>{this.props.children}</div>;
  }
}
```

> 注意
> 在 CSS 中，如果你不想要某个节点不作为布局的一部分，可以使用`display:Contents`属性。（这个应该也是提出了跳过 findDOMNode 的一个方案吧）

### 检测不安全的 side effects

### 检查过时的 Context API

## 用 PropTypes 来类型检查

## 不受控的组件

## Web 组件

# API 文档

## React 组件

### 介绍

#### 组件生命周期

#### 其他 API

#### 类属性

#### 实例属性

### 参考

#### 公共使用的生命周期

#### 渲染

#### 构造函数

#### componentDidMount

#### componentDidUpdate

#### componentWillUnmount

#### 很少使用的生命周期

#### shouldComponentUpdate

#### static getDerivedStateFromProps()

```
static getDerivedStateFromProps(props, state)
```

#### getSnapshotBeforeUpdate

#### Error boundaries

#### static getDerivedStateFromError()

#### componentDidCatch

#### 遗留的生命周期

#### UNSAFE_componentWillMount

#### UNSAFE_componentWillReceiveProps

#### UNSAFE_componentWillUpdate

### 其他 API

#### setState

#### forceUpdate

### 类属性

#### 默认属性

#### 显示名称

### 实例属性

#### 属性

#### 状态

## ReactDOM

### 介绍

#### 浏览器支持

### 参考

#### render()

#### hydrate()

#### unmountComponentAtNode()

#### findDOMNode()

> 注意：
> findDomNode 是用来访问底层 DOM 节点的应急方案。在大多数情况下，使用这个应急方案是不推荐的，因为它会破坏组件的抽象结构。[它已经在严格模式下废弃](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)

```
ReactDOM.findDOMNode(component)
```

如果组件已经在 DOM 中挂载，它会返回浏览器底层 DOM 元素。这个方法在读取 DOM 的值有用，向表单元素以及执行 DOM 测量。\*在大多数情况下，你可以附加 DOM 节点的引用来避免使用它\*\*
当组件渲染 null 或者 false，findDOMNode 会返回 null。当组件渲染 string,它会返回文本 DOM 节点的值。React16,组件可能是包含多个子节点的 fragment，在这种情况下，findDOMNode 将返回第一个非空子节点。

> findDOMNode 值在已挂载的组件中有效（组件已经在 DOM 节点中）。如果你在组件没有挂载的时候调用（比如在 render()函数中调用 findDOMNode()，组件没有创建的时候），会抛出异常。
> findDOMNode 不能再 function 组件中使用

#### createPortal()

## ReactDOMServer

## DOM Elements

## 合成 Event

## Test 工具

## Test Renderer

## JS 环境要求

## 词汇表

# HOOKS

## 介绍

## Hooks 一览

## 使用 State Hook

## 使用 Effect Hook

> Hooks 是 React 16.8 新加的。它可以不写 Class 也能用到 State 以及 React 的其他特性
> Effect Hook 让你在函数组件中执行副作用的操作

```
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
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

这个片段是基于上一页的计数器案例，但是我们向其中添加了新的特性。我们向网页设置包含点击次数的 title。
数据获取，设置订阅，或者手动在 React 组件中修改 DOM，这些都是边界影响的示例，或者说是效果。可能你在之前的组件中用过。（我的理解：纯函数组件，函数应该是无副作用的。但是网页组件是需要产生全局的影响，所以这类操作就称为副作用操作）。

> 小技巧
> 如果你熟悉 React 类声明周期函数，你可以考虑将 UseEffect 作为 componentDidMount, componentDidUpdate，componentWillUnmount 作为声明周期的联合。

### 不会清理副作用

有时候，我们想要在更新 DOM 之后运行一些额外的代码。网络请求，手动 DOM 更新，打印这些都是不需要清除副作用的案例。我们认为这些运行完之后可以立刻忘掉它们的存在。让我们来比较类和 HOOKS 的副作用的表现
**使用类的案例**
在 React 的类组件中，render 函数自身不会创建副作用。它应该更早-我们典型的想要将副作用的操作放到更新完 DOM 之后。
这也是为什么在 React 的类组件中，我们会将副作用操作放到`componentDidMount`以及`componentDidUpdate`中。回到我们的示例，在 React 变更 DOM 之后，立刻更新文档的 title。

```
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}
```

现在我们有两份重复的代码在两个生命周期函数中。
这也是为什么许多情况下，我们执行相同的副作用操作，不关心组件刚加载完或者刚更新 DOM 完。
从概念上将，我们向要在每次渲染之后执行-但是 React 类组件没有这样的方法。我们可以提取独立的方法，但是仍然会在两个地方调用。
现在让我们看下相同的事情在`UseEffect`HOOK 下的情况。
**使用 Hooks 的案例**
我们已经在上面看到过这个例子，但是让我们仔细看看

```
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
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

**useEffect 做了什么？**，使用这个 Hook，告诉 React 组件在每次渲染之后会执行。React 会记住你传的这个函数，然后在执行 DOM 更新之后调用。在这个副作用里，我们设置了页面的标题。但是我们也可以在其中去执行数据加载或者其他一些需要的 API
**为什么 useEffect 在组件中调用？**将`useEffect`放到组件中让我们可以在 effect 函数中访问到 state 变量或者任何其他的属性。我们不需要一个特殊的 API 来读取上面的变量-它早已存在是函数域。Hooks 拥抱 JS 闭包，并且避免再创建 React 的 API，因为 JS 已经提供的解决方案。
**useEffect 在每次渲染之后执行？**（我们稍后会讲述如果[自定义](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)它）。放弃去思考加载和更新概念，你将发现非常简单，副作用在每次渲染之后发生。React 保证 DOM 更新之后会运行它们的副作用函数。
**详细说明**
现在我们对副作用有了更多的了解，下面几行应该更有意义了。

```
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });
}
```

我们定义了`count`状态变量，然后我们告诉 React 使用一个副作用。我们传递一个`useEffect`Hook。这个函数中，我们使用浏览器 API 设置了`document.title`。我们可以在副作用函数中调用最新的`count`，因为它一个闭包函数中。当 React 渲染我们的组件，它会记住副作用函数，然后在每次更新完 DOM 之后调用。每次渲染之后包括第一次，都会执行它。
有经验的 JS 开发者会注意到每次渲染传递给`useEffect`的回调函数都有区别。这是故意的。这就是我们从函数内部读取数值的原因，不用担心它过时。每次渲染，我们都会创建不同的 effect 替换之前的 effect。某种情况下，它的行为更像是渲染的结果的一部分 - 每个 effect 属于特定的 render 函数。我们将在[此页面](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)后面讲述为什么这样做

> 技巧
> 不像`componentDidMount`和`componentDidUpdate`，`useEffect`不会阻塞浏览器更新屏幕。它会让你的应用感觉跟更加流畅。大部分 effect 不需要同步执行。但是某些情况需要这么做（比如测量布局），它被独立的抽成[useLayoutEffect](https://reactjs.org/docs/hooks-reference.html#uselayouteffect)Hook，与`useEffect`做区分。

### 清理副作用

早期，我们认为副作用不需要清理。但是有一些副作用需要。比如**我们需要设置订阅**一些额外的数据源。这种情况下，清理非常重要以免造成内存泄露！让我们比较类组件和 Hooks 是如何做的。
**使用类的案例**
在类组件中，你需要在`componentDidMount`设置订阅，然后在`componentWillUnmount`清理它。比如，假设有一个`ChatAPI`模块让我们订阅朋友线上状态。下面是我们用来订阅和显示状态的类：

```
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }

  render() {
    if (this.state.isOnline === null) {
      return 'Loading...';
    }
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```

通知的操作是一样的。生命周期强制分离了业务逻辑到不同函数功能下。

> 注意
> 聪明的读者可能注意到这个例子需要`componentDidUpdate`函数才完全正确。我们将会在[下一节](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)讨论到
> **使用 Hooks 的案例**
> 你可能会觉得我们需要一个独立的 effect 来执行清理。但是添加和删除订阅逻辑是强耦合的，useEffect 被设计来讲它们联系在一起。如果你返回一个函数，React 将会在合适的时机清理掉

```
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // Specify how to clean up after this effect:
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

**为什么我们需要在 effect 函数返回一个函数？**这是可选的机制。每个 effect 可能返回一个请求它的函数。这使得我们能够将添加和删除订阅保持在一起。也同样也是 effect 的一部分
**什么时候 React 会清理 effect？**React 会在组件卸载时清理。但是，前面的学习我们知道渲染不会只调用一次。这也是为什么 React 会在运行下一次 effect 之前清理上一次的 effect。我们后面将讨论[为什么会帮助我们避免 bug](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)和[在产生性能问题时如何退出此行为](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)

> 注意
> 我们不需要在 effect 返回有名函数。我们叫`cleanup`以描述意图，但是你也可以返回箭头函数或者叫其他函数名

### 概况

我们学习到组件渲染之后不同的 effect 表现。有些 effect 需要被清理，所以它返回一个函数。
其他 effect 不需要清理，所以不返回任何东西。
**如果你觉得你对 effect hook 工作有不错的理解，或者如果你感到不知所措，你可以跳到下一页 Hooks 的规则**

### 使用 Effects 的技巧

### 下一步

## Hooks 的规则

## 构建自己的 Hooks

## Hooks API 的文档

## Hooks 常见问题

# TESTING

# 并发模式（实验中）

# 贡献

# 常见问题
