---
title: react advance
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-09 01:12:36
password:
summary:
tags: [react, docs, 翻译, 进行中]
categories:
---

## 高级指南

### Accessibility

### 代码分割

#### 打包

大多数 React 应用使用 [Webpack](https://webpack.js.org/),[Rollup](https://rollupjs.org/)或者[Browserify](http://browserify.org/)工具来打包它们的文件。打包是跟踪导入文件并将它们合并到单个文件的过程：最后得到一个 bundle 文件。这个 bundle 文件可以放到页面上一次性加载整个应用。

#### Example

##### App:

```jsx
// app.js
import { add } from "./math.js";

console.log(add(16, 26)); // 42
```

```jsx
// math.js
export function add(a, b) {
  return a + b;
}
```

> 注意：
> 你的打包文件最后可能跟上面有点不一样。

如果你使用[Create React App](https://github.com/facebookincubator/create-react-app), [Next.js](https://github.com/zeit/next.js/), [Gatsby](https://www.gatsbyjs.org/),或者类似的工具，你将有一个 Webpack 配置开箱即用的打包你的应用。

如果你不是使用这些工具，你需要自己设置打包配置。举例，在 webpack 文档上看[安装](https://webpack.js.org/guides/installation/)和[入门指南](https://webpack.js.org/guides/getting-started/)。

#### 代码分割

打包是一个非常棒的技术，但随着你应用的增长，你打包的文件会增长的越大。尤其是你包含了非常大的第三方库时。你需要密切关注你打包的代码，以免意外使其过大导致你的应用需要花费很长时间区加载。

为了避免结成大包，前期应该思考问题并开始”分解“你的打包文件。代码分割是由像 Webpack，Rollup 以及 Browserify（factor-bundle）这样打包器支持的技术，能够创建多个包以便在运行时动态加载。

代码分割你应用可以帮助你懒加载用户当前需要的内容，它可以显著的提高你应用的性能。虽然并没有减少你应用的总代码行数，你可以避免加载到用户不需要的代码，减少在初始化加载时需要的代码。

#### import()

将代码分割引入到你应用的最好的方法是通过动态 import() 语法。
**Before**

```jsx
import { add } from "./math";

console.log(add(16, 26));
```

**After**

```jsx
import("./math").then((math) => {
  console.log(math.add(16, 26));
});
```

当 Webpack 遇到这个语法，它自动开始代码分割你的应用。如果你使用 Create React App，它已经为你配置好，你可以立刻[使用它](https://facebook.github.io/create-react-app/docs/code-splitting)。Next.js 同样也开箱即用的支持。

如果你自己设置 Webpack，你可能需要阅读 Webpack 的[代码分割指南](https://webpack.js.org/guides/code-splitting/)。你的 webpack 配置应该[像这样](https://gist.github.com/gaearon/ca6e803f5c604d37468b0091d9959269)。

当你使用[Babel](https://babeljs.io/)，你将需要保证 Babel 能够解析动态导入语法而不能够转化它。你需要[babel-plugin-syntax-dynamic-import](https://yarnpkg.com/en/package/babel-plugin-syntax-dynamic-import)来做到。

#### React.lazy

> 注意：
> React.lazy 和 Suspense 尚不能用于服务端渲染。如果你想要在服务端渲染应用中进行代码分割。我们建议使用 Loadable 组件。它提供了用于打包和服务端渲染拆分的非常好的指南。

React.lazy 函数可以让你将动态导入作为一个常规的组件。
**Before**

```jsx
import OtherComponent from "./OtherComponent";
```

**After**

```jsx
const OtherComponent = React.lazy(() => import("./OtherComponent"));
```

当这个组件被第一次渲染时它将自动加载包含 OtherComponent 组件的包。

React.lazy 接受一个必定调用动态 import()方法的函数。它一定会返回一个 Promise，这个 Promise 解析带有默认导出 React 组件的模块。

这个懒加载组件应该渲染在 Suspense 组件中，它允许我们去显示后备内容（比如加载中指示器）当我们等待懒加载组件去加载时。

```jsx
import React, { Suspense } from "react";

const OtherComponent = React.lazy(() => import("./OtherComponent"));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```

这个 fallback 属性接受任何你想在加载时显示的组件。你可以将 Suspense 组件放到懒加载组件之上的任何位置。你甚至可以用一个 Suspense 组件包装多个懒加载组件。

```jsx
import React, { Suspense } from "react";

const OtherComponent = React.lazy(() => import("./OtherComponent"));
const AnotherComponent = React.lazy(() => import("./AnotherComponent"));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```

#### 异常捕获边界

如果这个 other 模块加载失败（举例，由于网络失败），它将处罚一个异常。你可以处理这些错误去显示一个比较友好的用户体验并且使用[异常捕获边界](https://reactjs.org/docs/error-boundaries.html)来恢复。一旦你创建了你的错误异常边界，你可以在你懒加载组件之上任何地方显示错误状态，当发生网络异常时。

```jsx
import React, { Suspense } from "react";
import MyErrorBoundary from "./MyErrorBoundary";

const OtherComponent = React.lazy(() => import("./OtherComponent"));
const AnotherComponent = React.lazy(() => import("./AnotherComponent"));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
```

#### 基于路由的代码分割

决定应用中哪里引入代码分割可能比较棘手。你想要确保你选择的位置以均匀的分割包，但是不会破坏用户体验。

一个分割推荐的位置是根据路由。网络上大多数人习惯于页面过度需要花一些时间加载，你还倾向于重新渲染整个应用，因此你的用户可能无法同时跟页面上的其他元素交互了。

这里有一个实例使用[React Router](https://reacttraining.com/react-router/)和 React.lazy 来设置基于路由的代码分割到你的应用。

```jsx
import React, { Suspense, lazy } from "react";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";

const Home = lazy(() => import("./routes/Home"));
const About = lazy(() => import("./routes/About"));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home} />
        <Route path="/about" component={About} />
      </Switch>
    </Suspense>
  </Router>
);
```

#### 命名导出

React.lazy 当前支持默认导出。如果这个模块你想要使用命名导出，你可以创建一个中间模块重新将它作为默认导出。它能保证 tree shaking 正常工作，并且不会引入未使用的代码。

```jsx
// ManyComponents.js
export const MyComponent = /* ... */;
export const MyUnusedComponent = /* ... */;
```

```jsx
// MyComponent.js
export { MyComponent as default } from "./ManyComponents.js";
```

```jsx
// MyApp.js
import React, { lazy } from "react";
const MyComponent = lazy(() => import("./MyComponent.js"));
```

### Context

#### 什么时机使用 Context

#### 在使用 Context 之前

#### API

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

React 常见模式是一个组件返回多个元素。Fragments 让你给子元素分组，需要向 DOM 上添加额外节点。

```jsx
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```

这里有声明它的简短语法。

#### 动机

组件返回多个子元素是很常见的模式。看下面的 React 片段：

```jsx
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}
```

<Columns />应该需要返回多个<td>元素为了保证 HTML 渲染有效。如果父 div 被放到<Columns />的 render()函数中，HTML 的渲染结果将是无效的。

```jsx
class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```

<Table />的输出结果将是：

```jsx
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

Fragments 可以解决这个问题。

#### 使用

```jsx
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

它的结果是一个正确的<Table />输出。

```jsx
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>
```

##### 简短语法

这里有一个声明 fragments 的简单语法。它看起来像空的标签：

```jsx
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

你可以使用<></>跟其他元素一样，除了不支持 keys 和属性

##### 带 key 的 Fragments

显示的使用<React.Fragment>语法声明 Fragments 可以带 key。这个的一个使用场景是将数组映射成 fragments 数组--列如，创建一个描述列表：

```jsx
function Glossary(props) {
  return (
    <dl>
      {props.items.map((item) => (
        // 没有Key,React会触发key的警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

key 是为唯一可以传递给 Fragment 的属性。未来，我们可以添加额外的属性，比如事件监听函数。

### Higher-Order Components

### 集成其他库

### JSX in Depth

从根本上上来说，JSX 只是提供了 React.createElement(component, props, ...children)函数的语法糖。JSX 代码这样：

```jsx
<MyButton color="blue" shadowSize={2}>
  Click Me
</MyButton>
```

编译结果:

```jsx
React.createElement(MyButton, { color: "blue", shadowSize: 2 }, "Click Me");
```

如果它没有子元素可以使用自我闭合的标签形式，所以：

```jsx
<div className="sidebar" />
```

编译结果：

```jsx
React.createElement("div", { className: "sidebar" });
```

如果你想要测试某些具体的 jsx 语法编译成的 js 代码，你可以尝试使用[在线的 Babel 编译器](https://babeljs.io/repl/#?presets=react&code_lz=GYVwdgxgLglg9mABACwKYBt1wBQEpEDeAUIogE6pQhlIA8AJjAG4B8AEhlogO5xnr0AhLQD0jVgG4iAXyJA)

#### 指定 React 元素类型

JSX 标签第一部分决定了 React 元素的类型。

大写字母开头的类型表示这个 JSX 标签是一个 React 组件。这些标签会被编译成对这个命名的变量的直接饮用，所以如果你使用 <Foo /> 表达式，Foo 一定能够要在作用域内。

#### React 必须在作用域内

因为 JSX 编译成 React.createElement 的调用，React 库必须要包含在 JSX 代码的作用域内。

例如，虽然 React 和 CustomButton 没有被 js 直接引用，但是它们还是需要被导入：

```jsx
import React from "react";
import CustomButton from "./CustomButton";

function WarningButton() {
  // return React.createElement(CustomButton, {color: 'red'}, null);
  return <CustomButton color="red" />;
}
```

如果你不使用 JS 打包器而是用 `<script>` 标签挂载 React，它也会被在作用域挂载成 React 全局变量。

#### JSX 类型使用.符号语法

你也可以在 JSX 中使用.符号形式来引用一个 React 组件。

#### 用户定义的组件必须是大写开头

...

#### 在运行时选择类型

...

---

#### JSX 中的属性

#### JS 表达式作为属性

#### 字符串字面量

#### 属性默认是 True

#### 展开属性

---

#### JSX 的子元素

#### 字符串字面量

#### JSX 子元素

#### JS 表达式作为子元素

#### 函数作为子元素

#### Booleans, Null, and Undefined 会被忽略

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
