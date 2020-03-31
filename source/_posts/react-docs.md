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

# API 文档

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
