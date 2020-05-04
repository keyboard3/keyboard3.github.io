---
title: react docs
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-31 11:10:32
password:
summary:
tags: [react, 翻译, 进行中]
categories:
---

[React-Docs](https://reactjs.org/docs/getting-started.html)

## 开始

## 主要概念

## 高级指南

### Accessibility

### 代码分割

### Context

### Error Boundaries

### Forwarding Refs

Ref 转发是自动通过组件传递 ref 给它的子组件的一个技巧。它在应用中大多数组件是不需要使用的。但是，对于某些组件是需要的，尤其是重复使用的组件库。常见的场景描述如下。

#### 转发 ref 给 DOM 组件

来看`FancyButton`组件，它渲染原生`button`DOM 元素：

```jsx
function FancyButton(props) {
  return <button className="FancyButton">{props.children}</button>;
}
```

React 组件隐藏它们的实现，包括它们自己的渲染结果。其他组件使用`FancyButton`通常不需要获取 ref 来引用内部的`button`DOM 元素。这很好，因为它会阻止组件过度依赖它们的 DOM 结构。
尽管`FeedStory`或`Comment`这种封装对于应用级组件比较理想，但像`FancyButton`或`MyTextInput`这类高度复用的`叶子`组件不方便。这些组件倾向于在应用中像`button`和`input`这类常规 DOM 一样使用，访问这些 DOM 节点可能无法避免去管理焦点，选中或者动画。
**Ref 转发是一个可选特性，让组件可以获取 ref，并向下传递给子组件**
在下面的例子中，`FancyButton`使用`React.forwardRef`去获取`ref`传递它，然后将它传递给渲染的 DOM `button`。

```jsx
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

#### 组件库维护者注意

**当你在组件库中使用 ref 转发时，建议将它作为一个大版本修改**。这是因为你的库可能会观察到不同的行为（取决于你 ref 赋值给谁，它是什么类型），它会导致应用崩溃因为其他库都依赖旧的行为。
尽管`React.forwardRef`存在是允许有条件的使用，但也不推荐：它会改变你库的行为并且会造成他们升级 React 时，用户的应用被破坏。

---

#### 在高级组件中转发 ref

这个技巧对高级组件尤其有用（也叫做 HOC）。开始了解 logs 高阶组件，用于打印日志

```jsx
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log("old props:", prevProps);
      console.log("new props:", this.props);
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}
```

"logProps"高级组件将所有`props`传递给包裹的组件，所以渲染结果将会一致。比如，我们使用这个高阶组件去记录所有传递给`fancy button`组件的属性

```jsx
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

```jsx
import FancyButton from "./FancyButton";

const ref = React.createRef();

// 我们导入的FancyButton其实是LogProps高阶组件
// 它渲染的结果是一样的
// 我们的ref指向LogProps而不是内部的FancyButton
// 这意味着我们无法调用这类方法ref.current.focus()
<FancyButton label="Click Me" handleClick={handleClick} ref={ref} />;
```

幸运的是，我们可以通过`React.forwardRef`API 显示转发 ref 到内部的`FancyButton`组件上。`React.forwardRef`接受一个接收`props`和`ref`参数的渲染函数，并且返回 React 节点。例如：

```jsx
function logProps(Component) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log("old props:", prevProps);
      console.log("new props:", this.props);
    }

    render() {
      const { forwardedRef, ...rest } = this.props;

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

#### 在开发者工具中显示自定义名字

`React.forwardRef`接收一个渲染函数。 React 开发者工具用这个函数决定为转发组件显示的内容。
比如，下面的组件在开发者恐惧中将会显示"ForwardRef"

```jsx
const WrappedComponent = React.forwardRef((props, ref) => {
  return <LogProps {...props} forwardedRef={ref} />;
});
```

如果你命名了这个渲染函数，开发者工具将显示将包括这个名字（比如：”ForwardRef(myFunction)“）

```jsx
const WrappedComponent = React.forwardRef(function myFunction(props, ref) {
  return <LogProps {...props} forwardedRef={ref} />;
});
```

你可以设置函数的`displayName`属性包含这个组件的显示

```jsx
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

### Fragments

### Higher-Order Components

### 集成其他库

### JSX in Depth

### 优化性能

### Portals

### Profiler

### 没有 ES6 的 React

### 没有 JSX 的 React

### Reconciliation

### Refs 和 DOM

**Refs 提供了在渲染函数中访问 DOM 节点和 React 创建的元素的方法**
在典型 React 数据流中，`props`是父组件和子组件交互的唯一方法。为了修改子组件，你需要用新的属性重新渲染它。然而，某些情况下，在典型数据流之外修改子组件是势在必行的。这个被修改的子组件应该是 React 元素的实例，也可能是 DOM 元素。针对这两种情况，React 提供了应急方案。

#### 什么时候使用 Refs

下面有一些 Refs 的好的使用案例：

- 管理焦点，文本选中，或者媒体播放
- 强制触发动画
- 集成第三方的 DOM 库
  避免对那些可以用声明完成的东西上使用 refs
  比如，在 Dialog 组件上传递 isOpen 属性来代替调用 open()和 close()

#### 不要过度使用 Refs

你可能第一个想法使用 ref 来在 app 中实现。如果是这种情况，请花费一点时间思考它的状态应该属于组件树的哪一个层级。通常，将状态放到更高的层级上比较恰当。参考这个案例的[状态提升](https://reactjs.org/docs/lifting-state-up.html)的指南。

> 注意：
> 下面案例更新使用了 React 16.3 介绍的`React.createRef()`API。如果你使用了早期的 React,我们建议使用[callback refs](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)

#### 创建 Refs

使用`React.crateRef()`来创建 Refs，然后通过`ref`属性附加到 React 元素上。Refs 通常当组件被构建的时候将实例赋值给它，然后它们就在这种组件中被引用到。

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

#### 访问 Refs

当 render 中 ref 被传递给这个元素，这个节点的引用可以通过 ref 的 current 属性访问到。

```jsx
const node = this.myRef.current;
```

ref 根据 node 的类型不同值也不同：

- 当 ref 属性被用到 HTML 元素上，用`React.createRef`构建的 ref 接收底层 DOM 元素作为 current 属性。
- 当 ref 属性被使用到自定义类组件上，这个 ref 对象接收已挂载的组件实例作为 current 属性
- **你可能无法使用 ref 属性对函数组件**因为它们没有实例

下面的例子展示了差异

**添加 Ref 给 DOM 元素**
这个代码使用 ref 存储了一个 DOM 节点的引用

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // 创建ref来存储textInput DOM元素
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }

  focusTextInput() {
    // 明确使用input元素DOM API来获取文本输入焦点
    // Note: 我们访问current获取DOM节点
    this.textInput.current.focus();
  }

  render() {
    // 告诉React我们想要关联input的ref
    // 我们在构造函数中创建`textInput`
    return (
      <div>
        <input type="text" ref={this.textInput} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React 等到组件挂载后将 DOM 元素赋值到 ref 的 current 属性上，并且当组件卸载时会回退赋值 null。更新 ref 的时机发生在 componentDidMount 和 componentDidUpdate 生命周期之前。

**添加 ref 到类组件**
如果我们想要包装上面的 CustomTextInput，来模拟挂载之后立刻点击。我们可以使用 ref 获取自定义的 input 引用，然后手动调用它 focusTextInput 方法

```jsx
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return <CustomTextInput ref={this.textInput} />;
  }
}
```

注意只有 CustomTextInput 被声明为 class 才生效

```jsx
class CustomTextInput extends React.Component {
  // ...
}
```

**Refs 和函数组件**
默认情况下，你可能无法在函数组件上使用 ref，因为它没有实例

```jsx
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    //无法正常工作
    return <MyFunctionComponent ref={this.textInput} />;
  }
}
```

如果你想要别人从你的函数组件上获取 ref，你可以使用[forwardRef](https://reactjs.org/docs/forwarding-refs.html)(可以和[useImperativeHandle](https://reactjs.org/docs/hooks-reference.html#useimperativehandle)联合使用)，获取你可以将它转成类

你可以做到，然而在函数组件内部使用 ref 属性一样是指向 DOM 元素和 class 组件

```jsx
function CustomTextInput(props) {
  // textInput 一定要在这里定义，所以ref可以指向它
  const textInput = useRef(null);

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input type="text" ref={textInput} />
      <input type="button" value="Focus the text input" onClick={handleClick} />
    </div>
  );
}
```

#### 暴露 DOM Refs 给父组件

在极少数情况下，你可能想要访问从父组件中访问子 DOM 节点。通常不建议这么做，因为会破坏组件的封装，但是偶尔在子节点的触发获取焦点、测量大小或者位置非常有用。

你可以将 ref 添加到子组件上，它不是一个理想的方案。你可能只拿到 React 组件实例而不是 DOM 节点。另外，它在函数组价上不能正常工作。

如果你在 React16.3 及更高的版本，我们推荐使用[ref 转发](https://reactjs.org/docs/forwarding-refs.html)
.**ref 转发让组件可选暴露子组件的 ref 作为自己的 ref**。你可以从[在 ref 转发文档中](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)找到如何让子组件暴露给父组件的详细案例

如果你使用 React 16.2 及更低，或者你需要比提供 ref 转发更加灵活的能力，你可以使用[这个替代方案](https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509)，传递 ref 作为特殊名字属性来向下传递 ref。

可能的话，我们不建议暴露 DOM 节点，但是在应急的时候非常有用。注意这个方案需要你添加一些代码到子组件中。如果你对子组件的实现没有绝对的控制力，最后的选择是使用[findDOMNode](https://reactjs.org/docs/react-dom.html#finddomnode)，但是在严格模式下废弃且不推荐使用。

#### 回调 Refs

React 也支持"回调 refs"的方式来设置 refs，它让 refs 的设置和取消控制的粒度更细。

跟 createRef()创建的 ref 赋值给 ref 属性不一样，你需要传递给 ref 属性一个函数。这个函数接收 React 组件或者是 HTML 节点元素作为它的参数，可以将它存储下来在其他地方访问。

下面是通用案例：使用 ref 回调函数存储 DOM 节点引用到实例的属性上

```jsx
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);

    this.textInput = null;

    this.setTextInputRef = (element) => {
      this.textInput = element;
    };

    this.focusTextInput = () => {
      // 使用原生DOM API聚焦文本输入
      if (this.textInput) this.textInput.focus();
    };
  }

  componentDidMount() {
    // 在挂载的时候自动聚焦
    this.focusTextInput();
  }

  render() {
    // 使用 ref 回调保存文本输入节点的引用
    // 元素字段的实例(比如是 this.textInput).
    return (
      <div>
        <input type="text" ref={this.setTextInputRef} />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}
```

React 在组件挂载后将 DOM 元素传递给 ref 回调，然后当组件卸载时传递 null 给回调。在 componentDidMount 后者 componentDidUpdate 触发之前，Refs 会保证是最新的。

你可以在组件之间传递回调的 refs，跟用 React.createRef()方式创建的 Refs 对象一样

```jsx
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return <CustomTextInput inputRef={(el) => (this.inputElement = el)} />;
  }
}
```

在上面的例子中，Parent 将 ref 的回调函数 作为 CustomTextInput 的 inputRef 属性，然后这个 CustomTextInput 将这个函数传给<input>的属性。结果是，Parent 的 this.inputElement 将会被设置成与 CustomTextInput 的<input>元素相对应的 DOM 节点。

#### 过时的 API：String Refs

如果你之前使用过 React，你可能熟悉之前的 API，ref 的属性是 string。比如“textInput”，通过 this.refs.textInput 访问 DOM 节点。我们不建议使用 string 的 refs，它由[许多问题](https://github.com/facebook/react/pull/8333#issuecomment-271648615)，它已经过时，将在未来某个版本移除掉。

> 注意
> 如果你当时使用了 this.refs.txtInput 来访问 refs,我们建议使用另外的[回调方法](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)或者 [createRef API](https://reactjs.org/docs/refs-and-the-dom.html#creating-refs) 替换。

#### 注意 refs 的回调

如果 ref 的回调函数被定义成内联函数，它将会在更新期间被调用 2 次，首先是 null 然后是 DOM 元素。因为每次渲染会创建一个函数实例，所以 React 需要清除旧的 ref 并且设置一个新的。你可以将 ref 回调函数定义成绑定到 class 的函数来避免这个问题，但是注意大多数情况下不需要关心。

### Render Props

### 静态类型检查

### 严格模式

严格模式是为了突出应用中潜在的问题。像 Fragment 一样，严格模式不会渲染任何 UI。它会对后代元素进行额外的检查和警告。

> 注意：
> 严格模式检查只运行在开发模式下；它不会影响到生产环境构建
> 你可以在你的应用任何地方开启严格模式。比如

```jsx
import React from "react";

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

#### 识别不安全生命周期

#### 警告过使用时的字符串 refAPI

#### 警告使用废弃的 findDOMNode

React 支持使用 findDOMNode 给定类的实例去 DOM 树种找。正常情况下你不需要，因为你可以[直接向 DOM 节点附加 ref](https://reactjs.org/docs/refs-and-the-dom.html#creating-refs)
`findDOMNode`仍然可以在类组件中使用，但是它会破坏抽象，因为允许父组件能单独访问指定的已经渲染的子组件。它会造成重构困难，你不能改变组件的实现因为父组件可以访问到 DOM 节点。`findDOMNode`当 Fragment 包含多个子元素时，会只返回第一个非空节点。`findDOMNode`是一次阅读 API。当你访问时，它才会给你结果。如果子组件渲染了不同的节点，它无法识别这个变更。因此`findDOMNode`仅对单个不可变的组件上有效。
另外你可以显式的将 ref 传递给你自定义的组件，并使用[ref 转发](https://reactjs.org/docs/forwarding-refs.html#forwarding-refs-to-dom-components)传递给 DOM 节点上
你还可以给你的组件中包一个 DOM 节点，并直接附加上 ref

```jsx
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

#### 检测不安全的 side effects

#### 检查过时的 Context API

### 用 PropTypes 来类型检查

### 不受控的组件

### Web 组件

## API 文档

### React 组件

#### 介绍

##### 组件生命周期

##### 其他 API

##### 类属性

##### 实例属性

#### 参考

##### 公共使用的生命周期

##### 渲染

##### 构造函数

##### componentDidMount

##### componentDidUpdate

##### componentWillUnmount

##### 很少使用的生命周期

##### shouldComponentUpdate

##### static getDerivedStateFromProps()

```
static getDerivedStateFromProps(props, state)
```

##### getSnapshotBeforeUpdate

##### Error boundaries

##### static getDerivedStateFromError()

##### componentDidCatch

##### 遗留的生命周期

##### UNSAFE_componentWillMount

##### UNSAFE_componentWillReceiveProps

##### UNSAFE_componentWillUpdate

#### 其他 API

##### setState

##### forceUpdate

#### 类属性

##### 默认属性

##### 显示名称

#### 实例属性

##### 属性

##### 状态

### ReactDOM

#### 介绍

##### 浏览器支持

#### 参考

##### render()

##### hydrate()

##### unmountComponentAtNode()

##### findDOMNode()

> 注意：
> findDomNode 是用来访问底层 DOM 节点的应急方案。在大多数情况下，使用这个应急方案是不推荐的，因为它会破坏组件的抽象结构。[它已经在严格模式下废弃](https://reactjs.org/docs/strict-mode.html#warning-about-deprecated-finddomnode-usage)

```
ReactDOM.findDOMNode(component)
```

如果组件已经在 DOM 中挂载，它会返回浏览器底层 DOM 元素。这个方法在读取 DOM 的值有用，向表单元素以及执行 DOM 测量。\*在大多数情况下，你可以附加 DOM 节点的引用来避免使用它\*\*
当组件渲染 null 或者 false，findDOMNode 会返回 null。当组件渲染 string,它会返回文本 DOM 节点的值。React16,组件可能是包含多个子节点的 fragment，在这种情况下，findDOMNode 将返回第一个非空子节点。

> findDOMNode 值在已挂载的组件中有效（组件已经在 DOM 节点中）。如果你在组件没有挂载的时候调用（比如在 render()函数中调用 findDOMNode()，组件没有创建的时候），会抛出异常。
> findDOMNode 不能再 function 组件中使用

##### createPortal()

### ReactDOMServer

### DOM Elements

### 合成 Event

### Test 工具

### Test Renderer

### JS 环境要求

### 词汇表

## HOOKS

> Hooks 是 React 16.8 新加的。它可以不写 Class 也能用到 State 以及 React 的其他特性

### 介绍

```jsx
import React, { useState } from "react";

function Example() {
  // 声明一个新的状态变量，我们将会调用这个count
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

这个新的 useState 函数使我们学习的第一个“Hook”，但是这个案例只是演示。如果不理解不用担心。
**你可以在下一页学习 HOOKS**。这页，我们继续解释为什么我们给 React 给 HOOKS，它们怎么帮助你写下好的应用。

> 注意
> React 16.8 是支持 Hooks 的第一个版本。升级的时候，不要忘记升级所有的包，包括 React DOM。 React Native 从 0.59 版本支持 HOOKS

#### 视频介绍

在 React 2018 会议上，Sophie Alpert 和 Dan Abramov 介绍了 Hooks，跟着 Ryan Florence 演示如何使用它们去重构一个应用。看下面的视频

#### 不用破坏性改动

在我们继续之前，注意 Hooks：

- 完全可选。你可以只在一些组件中试用 Hooks,不用重新已存在的代码。如果你不想要你不用立刻学习和使用 Hooks。
- 百分百的向后兼容。Hooks 不包含任何破坏性改动
- 现在可用。Hooks 已经发布在 16.8 版本

**React 没有计划去删除类**。你可以在下面的章节中督导更多关于渐进策略。

**Hooks 不会影响你已有的 React 概念**。相反，Hooks 会为 React 概念提供更直接的 API。你已经知道的：属性，状态，上下文，引用，以及生命周期。稍后我们会提到，Hooks 也提供了更强大的方式来组合它们。

**如果你只想要学习 Hooks，直接跳到下一节！**你可以继续读这一页学习更多关于为什么我们添加 Hooks，以及我们如何使用它们不需要重写自己的应用

#### 动机

Hooks 解决了我们过去 5 年写过和维护的上千个 React 组件的各种不相关的问题。无论你在学习 React，还是每天使用，或者使用者类似的组件模型的框架，你可能遇到过这些问题。

#### 很难在组件之间复用状态逻辑

React 没有提供将可复用的行为附加到组件的方式（比如，链接到 Store）。如果你使用 React，你可能比较熟悉像[render props](https://reactjs.org/docs/render-props.html)和[higher-order components](https://reactjs.org/docs/higher-order-components.html)。但是这些方案需要使用它们需要重构你的组件，它们会使得你的代码非常难理解。如果你在 React DevTools 中观察过 React 应用，你将会发现由 Providers，consumers，higher-order 组件,render props，以及其他抽象包裹的组件回调地狱。尽管我们可以在[DevTools](https://github.com/facebook/react-devtools/pull/503)过滤它们，这些指向了一个更深的问题：React 需要为共享状态逻辑提供更好的原生途径。

使用 Hooks,你可以从组件里提取状态逻辑，使得它们可以独立测试和重复使用。**Hooks 允许你户需要你改变组件树结构来重用状态逻辑**。它使得在组件之间或者是在社区中共享状态逻辑变得简单。

我们在[构建自己的 Hooks](https://reactjs.org/docs/hooks-custom.html)讨论更多的内容

#### 复杂组件变得更难理解

我们经常开始维护一个简单的组件，但是逐渐状态变得难以管理且有许多副作用。每个生命周期函数经常不相关的逻辑混在一起。比如，组件可能在 componentDidMount 和 componentDidUpdate 中执行相同的获取数据操作。但是相同的 componentDidMount 函数也包含其他逻辑比如设置时间按监听，还需要在 componentWillUnmount 清理它。相互关联的代码需要在不同的地方一起变更，但是完全不相同的代码却要合并到一个方法里。这非常容易制造 bugs 和不一致的地方。

在许多情况下不可能将组件分割成更小，因为状态逻辑需要全局存在才行。这使得非常难以测试。这也是许多人更喜欢独立一个状态管理库的一个原因。然而，这些库会创造出太多的抽象，要求你在不同文件之间跳转，且使得组件之间复用更加困难。

为了解决它，**Hooks 让你基于相关联的逻辑将组件分割到更小的函数中（比如设置订阅和获取数据）**，而不是强制基于生命周期分割 diamante。你可以使用 reducer 管理组件的本地状态，让它变得可预测。

我们将在[Using the Effect Hook.](https://reactjs.org/docs/hooks-effect.html#tip-use-multiple-effects-to-separate-concerns)更多的讨论它们。

#### 类会使得人和机器弄混

除了会使得代码重用和 diamante 组织变得更困难之外，我们发现类是学习 React 的最大屏障。你需要理解`this`是如何在 JS 中工作的，它与许多语言的运行方式都不一样。你需要记着绑定事件处理函数。没有问题的[语法提案](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/)，代码也非常的冗余。人们很好的理解 props，state,以及从上而下的数据流，但是很难理解类。React 的 class 和 function 组件的区别，使用过的即使是有经验的开发者之间也会存在分歧。

另外，React 以及存在 5 年了，我们希望下一个 5 年也能够与时俱进。像[Svelte](https://svelte.dev/),[Angular](https://angular.io/),[Glimmer](https://glimmerjs.com/)以及其他展示的那样，组件的[预编译](https://en.wikipedia.org/wiki/Ahead-of-time_compilation)有许多潜力。尤其是在没有限制模板的时候。最近，我们一直用[Prepack](https://prepack.io/)实验[组件折叠](https://github.com/facebook/react/issues/7323),并且我们已经取得了初步成果。然而我们发现类组件会无意中鼓励一些开发范式使得一些优化方案回退到很慢的情况。类给现在的工具仍然会造成问题。比如，类无法压缩的不好，并且他会使得热加载不稳定。我们想要提供新的这个 API 使得代码能够更好的被优化。

为了解决这些问题，**Hooks 让你在不使用类的情况下获得更多的 React 特性**。概念上，React 组件已经非常接近函数。Hooks 拥抱函数，但是不会牺牲 React 实际的核心。Hooks 提供解决方案，并且不要求你学习复杂的功能或者响应式的编程技巧。

> 案例
> [Hooks 一览](https://reactjs.org/docs/hooks-overview.html) 是一个很好的学习 Hooks 的地方

#### 渐进兼容策略

> 总结：没有计划从 React 中删除类

我们知道 React 开发者更关注迭代产品,没有时间来看每个版本的新 api。Hooks 非常新，可能等更多的案例和教程出来之后再考虑学习和适配它们更好。

我们知道向 React 添加新的原生概念门槛非常高。对于更好奇的读者，我们准备了更加[详细的征求意见文档](https://github.com/reactjs/rfcs/pull/68)，来挖掘更详细的动机，并且提供了具体的设计决策和相关现有技术的额外视角。

**至关重要的是，Hooks 可以和现有代码同时工作，所以你可以渐进的使用它们**。不用赶着迁移 Hooks。我们建议编码任何“大的重写”，尤其是对已经存在的复杂的 class 组件。在我们的经验里，最好的练习方式是在首先在新的非核心组件中使用 Hooks，保证团队中的每个人都能够适应它。在你尝试 Hooks 之后，请随时发送邮件给我吗，无论积极和负面的。

我们意图让 Hooks 能够覆盖所用 class 的场景，但是我们仍然会对 class 支持。在 Facebook，我们有上千个用 class 的组件，我们绝对没有计划去重写它们。相反，我们会在新的代码中将 Hooks 与 class 一起使用。

### Hooks 一览

Hooks 是[向后兼容](https://reactjs.org/docs/hooks-intro.html#no-breaking-changes)。这个页面为有经验 React 用户提供了 Hooks 概览。

> 详细介绍
> 读这篇[动机](https://reactjs.org/docs/hooks-intro.html#motivation)来学习为什么我们要在 React 使用 Hooks。

#### State Hook

这个案例渲染了一个计数器。当你点击按钮，他就会增加值：

```jsx
import React, { useState } from "react";

function Example() {
  // 定义一个新状态变量，我们叫做count
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

这里, useState 是一个 Hooks（我们等会来讨论含义）。我们在函数组件中调用它，添加本地 state 进去。React 会在重复渲染的函数中保留它的状态。useState 返回一对：当前状态值和让你更新它的函数。你可以在事件处理器或者其他地方调用这个更新函数。它非常像 class 中的 this.setState，但是它不会合并旧值和新值。（我们展示了一个案例在[使用 State Hook](https://reactjs.org/docs/hooks-state.html)比较 useState 和 this.state）。

useState 只有一个参数是初始化值。在上面的案例中，它是 0 因为我们的计数器是从 0 开始。注意不像 this.state，这里的 state 不必是一个对象-虽然如果你想要可以是。初始的状态参数只有在第一次渲染期间使用。

**声明多个状态变量**
你可以在一个组件中多次使用 State Hook

```jsx
function ExampleWithManyStates() {
  // 定义多个状态变量
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState("banana");
  const [todos, setTodos] = useState([{ text: "Learn Hooks" }]);
  // ...
}
```

这个数组解构语法让我可以在调用 useState 时给状态变量取不同的名字。这些名字不是 useState Api 的一部分。相反，React 假设如果你调用 useState 多次，你在每次渲染时候是调用的相同的顺序。我们后面会讲到为什么它能工作并且什么时候有用。

那么什么是 Hook?
Hooks 是一个函数让你可以钩子钩进函数组件的状态和声明周期中的特性。Hooks 不在 class 中工作-让你不用 class 使用 React。（我们不建议全部重写现有的代码，但是你可以在新组件中使用它）

React 提供了一些内置的 Hooks 比如 useState。你也可以创建你自己的 Hooks 在不同的组件之间复用一些状态逻辑行为。我们将首先开内置的 Hooks。

> 详细说明
> 你可以在专用的页面[Using the State Hook.](https://reactjs.org/docs/hooks-state.html)来学习更多的 state Hook 知识。

#### Effect Hook

你之前可能已经执行过数据获取，订阅，或者手动在 React 组件中改变 DOM。我们叫这些行为为”副作用“（或者说是作用），因为它们可以影响其他组件并且在一次渲染过程中就结束。

这个 Effect Hook，useEffect，给函数组件添加执行副作用的能力。它跟 React class 中的 componentDidMount,componentDidUpdate,componentWillUnmount 用途相同，但是被统一到单个 API 中。（我们显示的案例比较了 useEffect 和在[Effect Hook 使用](https://reactjs.org/docs/hooks-effect.html)中的方法）

这个例子，这个组件在 React 更新 DOM 之后设置 document 的标题：

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

当你调用 useEffect，你告诉 React 在刷新变更到 DOM 之后运行你的”副作用“函数。Effects 在组件中被定义，所以它们可以访问组件的 props 和 state。默认情况下，React 在每次渲染之后运行 effects-包括第一次渲染（我们更多关于如何比较 class 的生命周期在[使用 Effect Hook 文章里](https://reactjs.org/docs/hooks-effect.html)）。

Effects 可以通过返回一个函数可选的指定如何清除它们。举例，这个组件使用 effect 来订阅用户在线状态，然后通过取消订阅来清除这个副作用。

```jsx
import React, { useState, useEffect } from "react";

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return "Loading...";
  }
  return isOnline ? "Online" : "Offline";
}
```

在这例子中，React 当在组件销毁时取消订阅我们的 ChatAPI，然后在后续渲时会跟之前一样重复运行这个副作用。（如果我们传递 props.friend.id 给 ChatAPI 没有任何变化，你也可以选择[告诉 React 跳过重新订阅](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)）

跟 useState 一样，你可以在单个组件中使用多个副作用：

```jsx
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...
```

Hooks 让你在一个组件中管理相关联的副作用。（比如添加和删除订阅），而不是强制基于生命周期函数分割逻辑。

> 详细解释
> 你可以在下面专用的页面[使用 Effect Hook](https://reactjs.org/docs/hooks-effect.html)来学习更多的 UseEffect

#### 构建自己的 Hooks

有时候，我们想要在组件之间重用状态逻辑。之前，有两种方案：[高阶组件](https://reactjs.org/docs/higher-order-components.html)和[渲染属性](https://reactjs.org/docs/render-props.html)。自定义 Hooks 可以做到，切不需要在组件树中添加更多的组件。

这页面刚开始，我们介绍了 FriendStatus 组件，它调用 useState 和 useEffect Hooks 去订阅好友的在线状态。我们想要在其他组件中也复用这个订阅逻辑。

首先，我们提取逻辑到自定义 Hook 叫 useFriendStatus:

```jsx
import React, { useState, useEffect } from "react";

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

它获取 friendID 作为参数，然后返回朋友是否在线状态。
现在我们可以在组件之间使用它：

```jsx
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return "Loading...";
  }
  return isOnline ? "Online" : "Offline";
}
```

```jsx
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? "green" : "black" }}>{props.friend.name}</li>
  );
}
```

这些组件内的状态完全隔离。Hooks 是一种复用状态逻辑的方式，而不是复用状态本身。实际上，每次调用 Hook 有完全独立的状态-所以甚至你可以在一个组件中调用多次自定义 Hook。

自定义 Hook 更像是一中约定而非功能。如果函数的名字以 use 开头并且他调用了其他 Hooks，我们就可以认你为它是自定义 Hook。这种 useSomething 的命名方式约定让 linter 插件可以找到使用 Hooks 代码的 bug。

你可以创建自定义 Hooks 覆盖表单处理，动画，订阅生命，计时器以及其他我们没想到的应用场景。我们非常期待 React 社区将会出现什么样的自定义 Hooks。

> 详细说明
> 你可以在指定文章[构建自己的 Hooks](https://reactjs.org/docs/hooks-custom.html)看到更多内容

#### 其他 HOOKS

这里有一些很少使用的内置 Hooks，你可能会觉得有用。比如，[useContext](https://reactjs.org/docs/hooks-reference.html#usecontext)让你不需要嵌套就可以订阅 React 上下文。

```
function Example() {
  const locale = useContext(LocaleContext);
  const theme = useContext(ThemeContext);
  // ...
}
```

[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)让你用 reducer 合并复杂组件的本地状态。

```
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer);
  // ...
```

> 详细说明
> 你可以在特定页面[Hooks API](https://reactjs.org/docs/hooks-reference.html)学到其他内置的 Hooks。

### 使用 State Hook

Hooks 的介绍章节使用了下面这个案例：

```jsx
import React, { useState } from "react";

function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

我们将通过比较同等效果的 class 案例的代码来学习 Hooks。

#### 同等的 class 案例

如果你之前在 React 中使用 class，下面的代码可能比较熟悉：

```jsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
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

状态初始是{count:0}，当用户每次点击按钮调用 this.setState()增长 state.count 的值。我们将在整个页面中使用这个 class 片段。

> 注意
> 你可能会疑惑为什么我们使用计数器来代替其他更加实用的例子。在学习 Hooks 的第一步，它帮助我们帮助更加关注 API 本身。

#### Hooks 和函数组件

复习一下，在 React 的函数组件像这样：

```jsx
const Example = (props) => {
  // You can use Hooks here!
  return <div />;
};
```

或者这样

```jsx
function Example(props) {
  // You can use Hooks here!
  return <div />;
}
```

你可能之前就已经知道这些叫做无状态组件。我们现在介绍如何在这里实用 React 状态，所以我们更喜欢称之为函数组件。

Hooks 无法在 class 中工作。但是你可以用它们来代替 class 组件。

#### 什么是 Hook？

我们的新案例在最开始的地方从 React 中导入 useState：

```jsx
import React, { useState } from "react";

function Example() {
  // ...
}
```

**什么是 Hook？** Hook 是一个特殊的函数让你可以钩子钩进 React 的特性。比如，useState 让你添加 React 状态到函数组件的 Hook。我们后面可以学习到其他 Hook。
**什么时候应该使用 Hook？** 之前你写一个函数组件意识到需要添加状态的时候，必须将它们转成 class。现在你可以在已存在的函数组件中使用 Hook。我们现在就可以使用它

> 注意
> 这里有一些特殊的规则告诉我们在一个组件中什么时候可以使用 useHook。去[Hooks 的规则](https://reactjs.org/docs/hooks-rules.html)学习它们。

#### 声明状态变量

在类中，我们通过在构造函数中设置 this.state 的值为{count:0}来初始化 count。

```jsx
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
```

在函数组件中，我们没有 this，所以我们无法赋值和读取 thi.state。但是我们可以直接在我们的组件中直接调用 useState Hook。

```jsx
import React, { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);
```

**调用 useState 做了什么?** 它声明了状态变量。我们变量叫做 count，但是我们可以叫其他任何值，比如 banana。这是一种在函数调用间保留变量的方式-useState 与 class 提供的 this.state 功能一样。通常，函数退出时变量消失，但是状态变量会被 React 保留。
**传递给 useState 需要哪些参数？** useState() Hook 的为一个参数是初始化的状态。不像 class，状态不必是一个对象。根据我们的需要可以是 number/string。在我们的案例里，我们使用 number 来记录用户点击了多少次，所以变量的初始状态是 0。（如果我们想在状态中存不同的变量，可以调用两次）
**useState 返回什么？** 它返回了一对值：当前状态和函数更新方法。这是为什么代码是`const [count, setCount] = useState()`。类似于 class 中的 this.state.count 和 this.setState。如果你不熟悉使用的语法，可以看这个[页面的底部](https://reactjs.org/docs/hooks-state.html#tip-what-do-square-brackets-mean)

现在我们知道 useState Hook 做了什么，我们的案例应该更加容易理解了。

```jsx
import React, { useState } from 'react';

function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);
```

我们声明了 count 的状态变量，并设置了 0。React 将记住重复渲染时的当前值，并且给最新的值给我们的函数。如果我们想要更新当前 count 值，可以调用 setCount。

> 注意
> 你可能疑惑：为什么 useState 不命名 createState？
> Create 不够精确，因为只有组件在第一次渲染时才需要创建。在下次渲染时，useState 给我们当前状态。否则它就不是 State 了。这也是为什么 Hook 的名字带 use 的原因。我们将在[Hooks](https://reactjs.org/docs/hooks-rules.html)学到为啥。

#### 读状态

当我们在 class 中显示当前 count 值是，我们读 this.state.count

```jsx
<p>You clicked {this.state.count} times</p>
```

在函数中，我们可以直接使用 count

```jsx
<p>You clicked {count} times</p>
```

#### 更新状态

在 class 中，我们需要调用 this.setState()来更新 count 值：

```jsx
<button onClick={() => this.setState({ count: this.state.count + 1 })}>
  Click me
</button>
```

在函数中，我们已经有了 setCount 和 count 变量，所以我们不需要 this。

```jsx
<button onClick={() => setCount(count + 1)}>Click me</button>
```

#### 概括

让我们现在来概括一下我们一行行的学习东西以及检查下我们的理解

```jsx
 1:  import React, { useState } from 'react';
 2:
 3:  function Example() {
 4:    const [count, setCount] = useState(0);
 5:
 6:    return (
 7:      <div>
 8:        <p>You clicked {count} times</p>
 9:        <button onClick={() => setCount(count + 1)}>
10:         Click me
11:        </button>
12:      </div>
13:    );
14:  }
```

- 第 1 行：我们从 React 中导入 useState。它让我们在函数组件中存储本地状态
- 第 4 行：在案例组件中，我们通过 useState Hook 来声明新的状态变量。它返回一对值，是我们给定的名称。我们叫变量为 count，因为它记录了按钮点击的次数。我们通过传 0 给 useState 作为初始状态。第二个返回的函数用来设置变量的。它使得我们可以更新 count，古名字为 setCount。
- 第 9 行：当我们点击，我们调用 setCount 来设置一个新值。React 将重新渲染 Example 组件，并使用最新的 count 变量。

刚开始看着可能有点多。不要着急！如果你不理解，你可以尝试从头到尾再读一遍。我们保证一旦里忘记 state 在 class 是如何工作的，你用新的眼光来看这些代码，将很容易理解了。

**提示: 中括号的含义？**
你可能注意到我们声明状态变量的时候用了中括号：

```jsx
const [count, setCount] = useState(0);
```

左边的名字不是 React API 的一部分。你可以命名自己的状态变量：

```jsx
const [fruit, setFruit] = useState("banana");
```

这个 js 语法叫做数组解构。它意味着我们创建了两个新的变量 fruit 和 setFruit，fruit 是 useState 返回的第一个值，setFruit 是第二个。等价代码如下。

```jsx
var fruitStateVariable = useState("banana"); // Returns a pair
var fruit = fruitStateVariable[0]; // First item in a pair
var setFruit = fruitStateVariable[1]; // Second item in a pair
```

当我们使用 useState 来声明状态变量，它返回一对-包含两个值的数组。第一个是当前值，第二个是更新它的函数。使用[0]和[1]来访问会造成一些困惑，因为它们有指定的含义。这也是为什么我们用数组解构来代替的原因。

**提示: 使用多个状态变量**
用[something,setSomething]来声明状态变量也是一个语法糖，因为如果我们想要更多，可以给状态变量指定不同的名字。

```jsx
function ExampleWithManyStates() {
  // Declare multiple state variables!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
```

在上面的组件中，我们有 age，fruit 和 todos 作为本地变量，并且我们可以独立的更新它们：

```jsx
function handleOrangeClick() {
  // Similar to this.setState({ fruit: 'orange' })
  setFruit("orange");
}
```

你不必使用很多状态变量。状态表变量也可以是一个对象或者数组，所以你可以将数据分组。然而不像 class 的 this.setState，更新状态会直接替换而不是合并。

我们提供了分割独立的状态变量更多的建议在[FAQ 中](https://reactjs.org/docs/hooks-faq.html#should-i-use-one-or-many-state-variables)

### 使用 Effect Hook

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

#### 不会清理副作用

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

#### 清理副作用

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

#### 概况

我们学习到组件渲染之后不同的 effect 表现。有些 effect 需要被清理，所以它返回一个函数。
其他 effect 不需要清理，所以不返回任何东西。
**如果你觉得你对 effect hook 工作有不错的理解，或者如果你感到不知所措，你可以跳到下一页 Hooks 的规则**

#### 使用 Effects 的技巧

我们将继续讨论 React 资深用户关心的 useEffect 深度内容。你不必现在就去了解它们。你可以随时查看这个页面学习 Effect Hook 的更多的信息。

**提示：使用多个 Effect 来分离关注点**
在 Hooks 的动机这篇文章中我们提到了一个问题就是类生命周期经常包含不相关的逻辑，但是相关的逻辑被破坏到几个不同的方法。下面的代码是将之前的计数器和好友状态指示器合并在了一起。

```jsx
class FriendStatusWithCounter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
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
  // ...
```

注意如何设置 document.title 的逻辑被分割到不同的函数中 componentDidMount 和 componentDidUpdate。订阅的逻辑同样被分割到 componentDidMount 和 componentWillUnmount 中。并且 componentDidMount 包含它们两个的代码。

所以，Hooks 应该如何解决这个问题？就像你可以使用多次 State Hook 一样，你可以使用多个 effects。它让我们分割不相关的代码到不同的 effects 中。

```jsx
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
  // ...
}
```

Hooks 让我们基于所做的事情不同分割，而不是生命周期函数。React 将根据每个组件指定的 effect 顺序应用每个 effect。

**解释： 为什么 Effect 运行在每次更新之后**
如果你使用过 class，你可能会疑惑为什么 effect 清除阶段会在每次渲染之后，而不是当销毁组件的时候。让我们看下一个特殊的实例，为什么这个设计帮助我们创建组件，却很少出现 bug。

在这一章开始的时候，我们介绍了实例 FriendStatus 组件显示朋友是否在线。我们的类从 this.props 读 friend.id,在组件创建之后订阅朋友的状态，在组件销毁之后取消订阅。

```jsx
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
```

当组件已经在前台页面，当用户属性发生变化时发生了什么？我们的组件将继续显示不同的好友的在线状态。这是一个 bug。在组件销毁时取消订阅错误的好友 ID，会造成对的好友在内存中，进行造成内存泄露。

```jsx
componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate(prevProps) {
    // Unsubscribe from the previous friend.id
    ChatAPI.unsubscribeFromFriendStatus(
      prevProps.friend.id,
      this.handleStatusChange
    );
    // Subscribe to the next friend.id
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
```

忘记正确的处理 componentDidUpdate 是 React 应用中最常见的来源。

现在来思考使用 Hooks 的组件版本

```jsx
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });
```

它不会遇到这个 bug。（虽然我们没有对它们做任何改动）

这里没有特殊的代码处理更新，因为 useEffect 默认处理掉了。它会在下一个 effect 应用之前清除上一个的 effects。为了说明这个，随着时间流逝组件会产生订阅和取消订阅的序列。

```jsx
// 挂载时的 { friend: { id: 100 } } 属性
ChatAPI.subscribeToFriendStatus(100, handleStatusChange); // 第一次运行effect

// 用{ friend: { id: 200 } }属性更新
ChatAPI.unsubscribeFromFriendStatus(100, handleStatusChange); // 清除之前的effect
ChatAPI.subscribeToFriendStatus(200, handleStatusChange); // 运行下一个

//用 { friend: { id: 300 } }属性更新
ChatAPI.unsubscribeFromFriendStatus(200, handleStatusChange); // 清除之前的effect
ChatAPI.subscribeToFriendStatus(300, handleStatusChange); // 运行下一个

// 销毁
ChatAPI.unsubscribeFromFriendStatus(300, handleStatusChange); // 清除最后一个
```

这个行为默认保证了一致性，阻止了类组件由于缺失更新逻辑导致的常见 bug。

**提示：通过跳过 Effects 来优化性能**
在某些情况下，每次渲染之后清除或者应用 effect 会造成性能问题。在 class 组件中，我们通过在 componentDidUpdate 写入一个额外的比较 prevProps 和 prevState 来解决这个问题：

```jsx
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

这是一个很常见的需求，内置到了 useEffect Hook API 中。如果你确认值在重复渲染时值没有发生变化，可以告诉 React 跳过应用 effect。为了做到它，可以传递一个数组作为 useEffect 的第二个可选参数：

```jsx
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // Only re-run the effect if count changes
```

在上面的例子中，我们传递[count]作为第二个参数。什么意思？如果 count 是 5,然后我们的组件内重复渲染时值仍然是 5，React 就会比较[5]和下一个渲染的[5]。因为数组的所有值都相等(5==5)，React 将会跳过 effect。这是实现了我们的优化。

当我们重新渲染时值更新为 6，React 将对比上一个渲染的数组[5]和下一个渲染的[6]。这个时候 5!==6，所以 react 将重新应用 effect。如果数组中有多项，只要其中一个不一样就会重新运行 effect。

对于有清除阶段的 effects 同样有效。

```jsx
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // 只有当值发生变化是才会重新订阅
```

未来，第二个参数可能在构建时期自动加上。

> 注意
> 如果你使用这个优化，保证数组包含组件内**随着时间变化并且被 effect 使用的所有值**。否则，你的代码将引用上一次渲染的泄露的值。参考[如何处理函数](https://reactjs.org/docs/hooks-faq.html#is-it-safe-to-omit-functions-from-the-list-of-dependencies)和[数组频繁变化是该怎么做](https://reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)学习更多相关内容。
> 如果你只想 effect 被运和清除一次（在挂载和销毁时），你可以传递一个空数组作为第二个参数。这样告诉 React 你的 Effect 不依赖属性或者 state，所以它不需要重新运行。这种并不属于特殊情况-它遵循依赖数组来工作
> 如果你传递一个空数组，在 effect 中的属性和状态将一直会有它们的初始值。当传递[]作为第二个参数非常接近 componentDidMount 和 componentWillUnmount 的心理模型。但是有[更好的方案](https://reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often)来避免 effect 被重复运行。另外，不要忘记 React 回延迟运行 useEffect 直到浏览器已经绘制，一次做额外的工作就不成问题了。
> 我们建议在 [eslint-plugin-react-hook](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 包中使用 [exhaustive-dps](https://github.com/facebook/react/issues/14920)规则。当警告依赖不正确并且提出修复意见。

#### 下一个步

恭喜！这是一个很长的页面，但是希望在结尾你的关于 effects 的问题都被解答了。你已经学习了 State Hook 和 Effect Hook,将它们结合起来可以做很多事情。它们覆盖了 class 的大部分场景-如果没有，你可以[其他 Hooks](https://reactjs.org/docs/hooks-reference.html) 找到帮助

我们也看到了 Hooks 如何解决了[动机](https://reactjs.org/docs/hooks-intro.html#motivation)提出的问题。我们看到 effect 如何避免在 componentDidUpdate 和 componentWillUnmount 的重复，将相关联的代码放在一起，帮助我们避免了许多 bugs。我们也看到了根据他们的功能来分离 effects，这写都是我们无法在 class 中做到的。

此时你可能好奇 Hooks 是如何工作的。React 是如何在重复渲染时调用 useSate 找到对应的 state 变量？React 如何匹配每次更新时上一个和下一个 effect？**下一节我们将学习 Hooks 的规则-这对用 Hooks 工作非常必要**

### Hooks 的规则

Hooks 是 JS 函数，但是当使用它们时你需要遵循两个原则。我们提供了[linter plugin](https://www.npmjs.com/package/eslint-plugin-react-hooks)来强制自动执行这些规则。

- 在最顶层的调用 Hooks
  **不要在循环，条件或者嵌套函数中调用 Hooks。**应该在你 React 函数最顶层的去使用 Hooks。遵循这个规则，你就能够确保 Hooks 在每次组件渲染时都会调用相同的顺序。让 React 能够在 useState 和 useEffect 多次调用之间正确的保留 Hooks 的状态。（如果你好奇，我们将在[下面](https://reactjs.org/docs/hooks-rules.html#explanation)深入的了解它们）

- 只在 React 函数中调用 Hooks
  **不要在常规的 js 函数中调用 Hooks**。相反，你可以：

  - ✅ 在 React 函数组件中调用 Hooks
  - ✅ 在自定义 Hooks 调用 Hooks（我们将在[下一页](https://reactjs.org/docs/hooks-custom.html)学习它们）

  遵循这条规则，确保在源码中组件的状态逻辑都是清晰可见的

#### ESLint Plugin

我们发布了 ESLint 插件叫做[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)强制遵循这两个规则。如果你喜欢尝试你可以添加插件到你的项目中：
在[Crate React APP](https://reactjs.org/docs/create-a-new-react-app.html#create-react-app)中默认包含这个插件。

```jsx
npm install eslint-plugin-react-hooks --save-dev
```

```jsx
//你的ESLint的配置
{
  "plugins": [
    // ...
    "react-hooks"
  ],
  "rules": {
    // ...
    "react-hooks/rules-of-hooks": "error", // Checks rules of Hooks
    "react-hooks/exhaustive-deps": "warn" // Checks effect dependencies
  }
}
```

你可以跳过直接去下一章，怎么写自己的 Hooks。在这个页面，我们将继续解释这些规则背后的原因。

#### 解释

在学习初期，我们在一个组件中使用了多个 State 和 Effect。

```jsx
function Form() {
  // 1. Use the name state variable
  const [name, setName] = useState("Mary");

  // 2. Use an effect for persisting the form
  useEffect(function persistForm() {
    localStorage.setItem("formData", name);
  });

  // 3. Use the surname state variable
  const [surname, setSurname] = useState("Poppins");

  // 4. Use an effect for updating the title
  useEffect(function updateTitle() {
    document.title = name + " " + surname;
  });

  // ...
}
```

所以 React 是怎么知道哪个 state 对应哪个 useSate 调用？答案是**React 依赖 Hooks 被调用的顺序**。在我们的例子中，因为每次渲染 Hooks 的调用顺序都是相同的所以能够正常工作。

```jsx
// ------------
// First render
// ------------
useState("Mary"); // 1. Initialize the name state variable with 'Mary'
useEffect(persistForm); // 2. Add an effect for persisting the form
useState("Poppins"); // 3. Initialize the surname state variable with 'Poppins'
useEffect(updateTitle); // 4. Add an effect for updating the title

// -------------
// Second render
// -------------
useState("Mary"); // 1. Read the name state variable (argument is ignored)
useEffect(persistForm); // 2. Replace the effect for persisting the form
useState("Poppins"); // 3. Read the surname state variable (argument is ignored)
useEffect(updateTitle); // 4. Replace the effect for updating the title

// ...
```

在多次渲染函数之间 Hooks 的调用顺序一样，React 就可以关联相同的本地 State 给每个 Hooks。但是如果我们将 Hook 调用放到判断条件中会发生什么？

```jsx
// 🔴 在条件判断中使用Hook将违反了第一个规则
if (name !== "") {
  useEffect(function persistForm() {
    localStorage.setItem("formData", name);
  });
}
```

第一次渲染 name !== ""是 true，所以我们会运行这个 Hook。但是在下一次渲染的时候，用户可能清除了这个表单，使得这个条件 false。现在在渲染的时候跳过这个 Hook 了，Hook 的调用顺序发生了变化：

```jsx
useState("Mary"); // 1. 读名为name状态变量 (参数被忽略)
// useEffect(persistForm)  // 🔴 这个Hook被跳过
useState("Poppins"); // 🔴 2 (but was 3). 读取名为surname的状态变量失败
useEffect(updateTitle); // 🔴 3 (but was 4). 替换effect失败
```

React 不知道第二个 useState Hook 调用返回的结果是什么。React 预期在这个组件中第二个 Hook 调用应该是 persisForm effect，就像之前运行一样，但是现在不是。从这点开始，我们跳过的这个后面的每个 Hook 调用都会被提升一级，导致 bugs。

**这也是为什么 Hooks 一定要在组件的顶层调用。**如果我们想要有条件的运行 effect，我们可以在 Hook 中设置条件。

```jsx
useEffect(function persistForm() {
  // 👍 我们没有违反第一个规则
  if (name !== "") {
    localStorage.setItem("formData", name);
  }
});
```

**注意，如果你装了插件就不需要担心这个问题**。不过你现在知道了为什么 Hooks 这么工作，也知道了这个规则是为了避免什么问题。

#### 下一步

最后，我们准备开始学习些构建自己的 Hooks！自定义 Hooks 让你可以组合 React 提供的 Hooks 到那你自己的抽象，让它可以在组件之间复用状态逻辑。

### 构建自己的 Hooks

构建自己的 Hooks 让你可以提取组件的逻辑到可重用的函数。

当我们学习 Effect Hook 时，我们看到聊天应用的组件显示消息来指示朋友是在线还是离线：

```jsx
import React, { useState, useEffect } from "react";

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return "Loading...";
  }
  return isOnline ? "Online" : "Offline";
}
```

现在让我们来看，聊天应用还有一个聊天列表，我们想要渲染在线用户端名字为绿色。我们可以 cv 相同的逻辑到 FriendListItem 项中，但是这不够理想。

```jsx
import React, { useState, useEffect } from "react";

function FriendListItem(props) {
  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  return (
    <li style={{ color: isOnline ? "green" : "black" }}>{props.friend.name}</li>
  );
}
```

相反，我们希望在 FriendStatus 和 FriendListItem 之间共享这个逻辑。

在这之前，React 在组件之间有两种方式共享状态逻辑：[渲染属性](https://reactjs.org/docs/render-props.html) 和[高阶组件](https://reactjs.org/docs/higher-order-components.html)。我们将看到 Hooks 如何不在组件树中添加更多的组件解决这些问题。

#### 提取自定义 Hook

当我们想要在两个 js 函数中共享逻辑时，我们提取它到第三方函数中。组件和 Hooks 都是函数，所以这同样可以生效！

**自定义 Hook 是一个使用 use 开头的 js 函数并且可以调用其他 Hooks。**列如，下面 useFriendStatus 是我们第一个自定义 Hook。

```jsx
import { useState, useEffect } from "react";

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

这里面没有新的的东西--逻辑都是从上面的组件中 copy 过去的。就像在组件中，保证只在自定义 Hook 顶层调用其他 Hook。

不像 React 组件，自定义 Hook 不需要有具体签名。我们可以自己决定用什么参数，参数是什么，以及该返回什么。其他方面就像一个普通的函数。函数命名一直以 use 开头，一眼就可以看出符合 Hooks 的规则。

useFriendStatus Hook 的目的是为了订阅朋友的状态。这也会为什么第一给参数是 friendID，返回这个朋友是否在线。

```jsx
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  return isOnline;
}
```

现在让我们来看下如何使用我们自己的自定义 Hook

#### 使用自定义 Hook

我们开始的目标是从 FriendStatus 和 FriendListItem 组件中删除重复逻辑。它们两个都想知道朋友是否在线。
现在我们可以提取逻辑到 useFriendStatus Hook，然后就可以使用它。

```jsx
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return "Loading...";
  }
  return isOnline ? "Online" : "Offline";
}
```

```jsx
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? "green" : "black" }}>{props.friend.name}</li>
  );
}
```

**这代码是否等价于原来的案例？** 是的，它们相等。如果你仔细观察，你将注意到我们不会更改任何行为。我们只是从两个函数中提取公共的代码到独立的函数中。**自定义 Hooks 是自然遵循 Hooks 设计的约定，而不是 React 特性。**

**自定义 Hook 命名开始都必须带 use 吗？** 必须。这个约定很重要。没有它，我们无法自动检查 Hooks 的规则违规，因为我们无法知道那个函数包含了 Hooks 调用。

**在两个组件使用相同的 Hook 会共享装填吗？** 不会。自定义 Hooks 是重用状态逻辑的机制（像设置订阅和记住当前值），但是你每次使用自定义 Hook，所有其中的状态和 effects 都是完全独立的。

**自定义 Hook 如何获得独立的状态？** 每次调用 Hook 获得独立的状态。因为直接调用 useFriendStatus，从 React 的视角我们的组件只是调用了 useState 和 useEffect。跟我们之前了解的一样，我们可以在一个组件中调用 useState 和 useEffect 多次，它们是完全独立的。

**提示：在 Hooks 之间传递信息**
因为 Hooks 是一个函数，我们可以在它们之间传递信息。

为了说明这一点，在我们假想的聊天案例中使用其他组件。它是一个聊天消息接收者选择器，显示那些当前选中好友是否在线：

```jsx
const friendList = [
  { id: 1, name: "Phoebe" },
  { id: 2, name: "Rachel" },
  { id: 3, name: "Ross" },
];

function ChatRecipientPicker() {
  const [recipientID, setRecipientID] = useState(1);
  const isRecipientOnline = useFriendStatus(recipientID);

  return (
    <>
      <Circle color={isRecipientOnline ? "green" : "red"} />
      <select
        value={recipientID}
        onChange={(e) => setRecipientID(Number(e.target.value))}
      >
        {friendList.map((friend) => (
          <option key={friend.id} value={friend.id}>
            {friend.name}
          </option>
        ))}
      </select>
    </>
  );
}
```

我们存当前选中的好友 ID 到 recipientID 的状态变量上，我们可以传递它到我们自定义的 useFriendStatus Hook 的参数上。

```jsx
const [recipientID, setRecipientID] = useState(1);
const isRecipientOnline = useFriendStatus(recipientID);
```

它让我们知道当前选中的朋友是否在线。如果我们选中另外一个好友，然后会更新这个 recipientID 状态变量，我们 useFriendStatus Hook 将之前的选中的好友取消订阅，并订阅当前新选中的一个。

#### useYourImagination()

自定义 Hooks 提供了之前无法在 React 上实现的灵活共享状态逻辑。你可以自定义 Hooks 来覆盖更大范围的用户场景，比如表单处理，动画，声明订阅，定时器和可能更多我们无法考虑到的场景。更重要的是，你创建 Hooks 就像使用 React 内置特性一样简单。

尽量避免过早的添加抽象。现在函数组件可以做的更多，那么你代码仓库中函数组件的代码将会边长。这属于正常现象--不要觉得你要立刻将它拆分到 Hook 中。但我们仍鼓励你发现自定义 Hook 可以将复杂的逻辑隐藏在简单的接口后面，或者帮助解开组件杂乱的情况。

举例，可能你有个复杂的组件，包含大量用特殊方式管理的本地状态。useState 不会使得集中化的更新逻辑简单，所以你应该更愿意使用 Redux 的 reducer 来编写：

```jsx
function todosReducer(state, action) {
  switch (action.type) {
    case "add":
      return [
        ...state,
        {
          text: action.text,
          completed: false,
        },
      ];
    // ... other actions ...
    default:
      return state;
  }
}
```

Reducers 非常方便的独立测试，并且易于扩展用于表达复杂的更新逻辑。如果需要你可以更进一步的拆分成更小的 reducers。但是，你可能还享受这 React 本地状态带来的好处，或者不想安装其他的库。

所以为什么不可以写一个 useReducer Hook，让它来帮助我们用 reducer 来管理我们组件的本地状态？一个它的简单版本可以像这样：

```jsx
function useReducer(reducer, initialState) {
  const [state, setState] = useState(initialState);

  function dispatch(action) {
    const nextState = reducer(state, action);
    setState(nextState);
  }

  return [state, dispatch];
}
```

现在我们可以使用它在我们的组件中，让 reducer 驱动它的状态管理。

```jsx
function Todos() {
  const [todos, dispatch] = useReducer(todosReducer, []);

  function handleAddClick(text) {
    dispatch({ type: "add", text });
  }

  // ...
}
```

在复杂的组件中使用 reducer 来管理本地状态是常见的需求，我们已经在 React 中内置了 useReducer Hook。你可以在[HOOKS API 文档](https://reactjs.org/docs/hooks-reference.html)中找到这些 Hook

### Hooks API 的文档

如果你是 Hooks 的新手，你应该首先查看[概览](https://reactjs.org/docs/hooks-overview.html)。你也可以在 Hooks 中找到有用的信息。

#### 基础的 Hooks

##### useState

```jsx
const [state, setState] = useState(initialState);
```

返回一个有状态值，已经一个更新它的函数。

在初始化渲染期间，这个返回的 state 就是初始传参 initialState。

这个 setState 函数是用来更新 state 的。它接收新状态值并触发组件重新渲染。

```jsx
setState(newState);
```

在重新渲染期间，useState 返回的第一个值是更新 state 之后的最新值。

> 注意
> React 保证它的 setState 函数可以稳定的识别，并且不会再重新渲染时发生改变。这是为什么可以安全的从 useEffect 或者 useCallback 的依赖列表中省略 setState 了。

**函数式更新**
如果新的状态是由上个状态更新出来的，你可以传递一个函数给 setState。函数接受之前的值，然后返回更新值。这里有一个计数器组件的案例展示了两用使用 setState 的方法。

```jsx
function Counter({ initialCount }) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount((prevCount) => prevCount - 1)}>-</button>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>+</button>
    </>
  );
}
```

+和-按钮使用了函数形式，因为更新值是基于前面值。但是重置按钮使用了常用形式，因为它经常设置 count 返回初始值。

如果你的更新函数对比当前状态返回了相同的值，随后重新渲染将会完全跳过。

> 注意
> 不像在类组件中的 setState 方法，useSate 不会自动合并对象。你可以通过用对象展开语法的更新函数来复制这个行为。

```jsx
setState((prevState) => {
  // Object.assign would also work
  return { ...prevState, ...updatedValues };
});
```

另外选项是 useReducer，它更适合管理包含多个子值的状态对象。

**延迟的初始状态**
initialState 参数是在初次渲染期间作为 state。在后续渲染时会被忽略。如果初始状态结果是非常昂贵的技术，你可能需要提供函数来替换，它只有在第一次渲染时才会被执行。

```jsx
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

**跳过状态更新**

如果你更新 State Hook 给一个当前状态相同的值，React 将跳过渲染子组件或者触发 effects。（React 使用）[Object.is 来比较计算](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description)

注意 React 仍然可能在跳过更新之前渲染该组件。大可不必担心，因为 React 不会对组件树进行深度对比。如果你在渲染期间做了很昂贵的计算，你应该使用 useMemo 进行优化。

##### useEffect

```jsx
useEffect(didUpdate);
```

**清理 effect**
**effects 的执行时机**
**有条件的触发 effect**

##### useContext

```jsx
const value = useContext(MyContext);
```

**将它和 Context.Provider 放在一起**

#### Additional Hooks

##### useReducer

##### useCallback

##### useMemo

##### useRef

##### useImperativeHandle

##### useLayoutEffect

##### useDebugValue

### Hooks 常见问题

## TESTING

## 并发模式（实验中）

## 贡献

## 常见问题
