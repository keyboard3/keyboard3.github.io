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

[原文](https://reactjs.org/docs/accessibility.html)

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

> Context 提供了通过组件树传递数据的方式，不需要手动在各个 level 手动向下传递 props。

在典型的 React 应用中，数据是通过 props 从上自下传递的（父亲传递给孩子），但是对于应用中的许多组件的 props(比如，本地偏好，UI 主题)需要确定类型，可能会很麻烦。Context 提供了在组件之间共享值的方式，不需要显式的在树结构的每个 level 上传递 props。

#### 什么时机使用 Context

Context 被设计共享那些 React 组件树种被认为是”global“的数据，例如当前经过身份认证的用户，主题，或者语言偏好。例如，下面的代码我们手动通过主题穿线为 Button 组件设置样式。

```jsx
class App extends React.Component {
  render() {
    return <Toolbar theme="dark" />;
  }
}

function Toolbar(props) {
  // Toolbar组件一定要获取theme prop，并且传递它到ThemeButton。它很有用
  // 如果应用中的每个Button都需要知道theme。因为它需要给所有组件都要传递theme
  return (
    <div>
      <ThemedButton theme={props.theme} />
    </div>
  );
}

class ThemedButton extends React.Component {
  render() {
    return <Button theme={this.props.theme} />;
  }
}
```

使用 context，我们可以避免通过中间元素传递 props。

```jsx
// 上下文让我们将值传递到组件树深处
// 不需要显式的串联每个组件
// 为当前主题创建上下文 (默认使用light).
const ThemeContext = React.createContext("light");

class App extends React.Component {
  render() {
    // 下面使用Provider传递当前的主题到组件树中
    // 任何组件都可以读到，不要关心组件树有多深
    // 在这个例子中，我们将传递dark作为当前值
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件不在需要显式的向下传递theme
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 分配一个contextType去读当前主题的context.
  // React将找到最近的主题provider，然后使用这个值
  // 在这个例子中，当前主题是dark
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}
```

#### 在使用 Context 之前

Context 主要用于需要嵌套在不同层级的组件都可以访问的一些数据。要谨慎的使用它，因为它会使得组件复用更加困难。

**如果你只想避免给许多层级传递相同的 props，[组件组合](https://reactjs.org/docs/composition-vs-inheritance.html)相较于 Context 是一个常用的简单解决方案**

举例，思考 Page 组件向下穿透几个层级传递 user 和 avatarSize 属性，让深层次的 Link 和 Avatar 组件可以读到它：

```jsx
<Page user={user} avatarSize={avatarSize} />
// ... which renders ...
<PageLayout user={user} avatarSize={avatarSize} />
// ... which renders ...
<NavigationBar user={user} avatarSize={avatarSize} />
// ... which renders ...
<Link href={user.permalink}>
  <Avatar user={user} size={avatarSize} />
</Link>
```

如果只有最后的 Avatar 组件读，可能会觉得通过许多中间层次的向下传递 user 和 avatarSize 有些多余。当 Avatar 组件需要从顶部中获得更多的属性时就非常烦人了，你必须在所有中间层中添加它们。

一种不带 context 的解决这个问题的方法是[向下传递 Avatar 组件自己](https://reactjs.org/docs/composition-vs-inheritance.html#containment)，所以中间层级组件不需要知道 user 或者 avatarSize 属性。

```jsx
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// Now, we have:
<Page user={user} avatarSize={avatarSize} />
// ... which renders ...
<PageLayout userLink={...} />
// ... which renders ...
<NavigationBar userLink={...} />
// ... which renders ...
{props.userLink}
```

随着这次改变，只有顶层的 Page 组件需要知道 Link 和 Avatar 组件使用 user 和 avatarSize 属性。

在许多情况下，通过减少应用需要传递的 props 以及让顶层组件有更多的控制力，这种倒置的控制可以让你的代码清晰。然而它不是每种情况下正确的选择：将树中复杂度提高会使得更高级别组件变得更加复杂，并且会强制低级别组件变得比你想象的还要灵活。

你不仅可以给组件传递单个 child。你可能要传递多个 children，或者给子组件提供多个单独的”插槽“，[如下所示](https://reactjs.org/docs/composition-vs-inheritance.html#containment)

```jsx
function Page(props) {
  const user = props.user;
  const content = <Feed user={user} />;
  const topBar = (
    <NavigationBar>
      <Link href={user.permalink}>
        <Avatar user={user} size={props.avatarSize} />
      </Link>
    </NavigationBar>
  );
  return <PageLayout topBar={topBar} content={content} />;
}
```

在多数情况下，当你需要从它的中间父亲中解耦出 child，这种模式足够了。如果 child 需要在渲染之前和父组件交流，可以使用[render props](https://reactjs.org/docs/render-props.html)进一步完善它。

但是，有时候一些数据需要被树中许多组件访问，且嵌套在不同的层级中。Context 让你可以”广播“这种数据及其更改到下面的所有组件。使用 context 常见的案例包括管理当前的 local,theme，或者一些缓存数据，这比替代方案要简单的多。

#### API

##### React.createContext

```js
const MyContext = React.createContext(defaultValue);
```

创建一个 Context 对象。当 React 渲染一个订阅该 Context 对象的组件时将从树中它的上面最近匹配的 Provider 读取值。

这个 defaultValue 参数只有当组件没有在树中它的上级匹配到 provider 时使用。不用 Provider 包装，有助于隔离测试。注意：给 Provider 的 value 传递 undefined 时， 消费组件不会使用 defaultValue。

##### Context.Provider

```jsx
<MyContext.Provider value={/* some value */}>
```

每个 Context 对象都带有一个 ProviderReact 组件，它允许消费组件去订阅 context 变化。

接受 value 属性传递给当前 Provider 的后代消费组件。一个 Provider 可以连接多个消费组件。Providers 可以嵌套，深层的会覆盖上层的。

Provider 的 value 属性变化，它的的所有后代消费组件都重新渲染。Provider 传递给它的后代消费者的传播不是订阅自 shouldComponentUpdate 方法，所以即使当消费组件的祖先组件跳过一个更新时，它仍然会根据这个传播来更新。

更改的依据对比新值和旧值的方式使用了同样的算法,[Object.is](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is#Description)

> 注意：
> 当传递 value 是对象时，它的变更会带来一些问题：看[注意事项](https://reactjs.org/docs/context.html#caveats)

##### Class.contextType

```jsx
class MyClass extends React.Component {
  componentDidMount() {
    let value = this.context;
    /* 使用MyContext的值在挂载组件之后做一些副作用操作 */
  }
  componentDidUpdate() {
    let value = this.context;
    /* ... */
  }
  componentWillUnmount() {
    let value = this.context;
    /* ... */
  }
  render() {
    let value = this.context;
    /* 根据 MyContext的值进行渲染 */
  }
}
MyClass.contextType = MyContext;
```

在 Class 上的 ContextType 属性可以分配由[React.createContext()](https://reactjs.org/docs/context.html#reactcreatecontext)创建的 Context 对象。它让你使用 this.context 来消费这个 Context Type 最近的值。在在任何生命周期函数中引用它，包括渲染方法。

> 注意：
> 你只能使用这个 API 订阅单个 context。如果你需要了解更多读[消费多个 Context](https://reactjs.org/docs/context.html#consuming-multiple-contexts)。

> 如果你使用实验性的[public class fields syntax](https://babeljs.io/docs/plugins/transform-class-properties/),你可以使用 static 类字段来初始化你的 contextType。

```jsx
class MyClass extends React.Component {
  static contextType = MyContext;
  render() {
    let value = this.context;
    /* 根据这个值来渲染 */
  }
}
```

##### Context.Consumer

```jsx
<MyContext.Consumer>
  {value => /* 根据这个值来渲染东西 */}
</MyContext.Consumer>
```

订阅 context 变化的 React 组件。让让你用函数组件订阅一个 context。

要求一个[函数作为 child](https://reactjs.org/docs/render-props.html#using-props-other-than-render)。这个函数接受当前 context 并返回 React node。这个传递给函数的 value 参数等于树结构中上面最近的 Provider 提供的 Context。如果没有找到匹配的 Provider，这个 value 参数就等于默认传递给 createContext()的 defaultValue。

> 注意：
> 关于‘函数作为 child’模式的更多信息，看[render props](https://reactjs.org/docs/render-props.html)

##### Context.displayName

Context 对象接受 displayName 字符串属性。React DevTools 使用这个字符串决定 context 显示的什么。

举例，下面的组件将在 DevTools 中显示 MyDisplayName：

```jsx
const MyContext = React.createContext(/* some value */);
MyContext.displayName = 'MyDisplayName';

<MyContext.Provider> // "MyDisplayName.Provider" in DevTools
<MyContext.Consumer> // "MyDisplayName.Consumer" in DevTools
```

#### 实例

##### 动态 Context

带主题动态值的复杂实例
**theme-context.js**

```jsx
export const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee",
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222",
  },
};

export const ThemeContext = React.createContext(
  themes.dark // default value
);
```

**themed-button.js**

```jsx
import { ThemeContext } from "./theme-context";

class ThemedButton extends React.Component {
  render() {
    let props = this.props;
    let theme = this.context;
    return <button {...props} style={{ backgroundColor: theme.background }} />;
  }
}
ThemedButton.contextType = ThemeContext;

export default ThemedButton;
```

**app.js**

```jsx
import { ThemeContext, themes } from "./theme-context";
import ThemedButton from "./themed-button";

// An intermediate component that uses the ThemedButton
function Toolbar(props) {
  return <ThemedButton onClick={props.changeTheme}>Change Theme</ThemedButton>;
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      theme: themes.light,
    };

    this.toggleTheme = () => {
      this.setState((state) => ({
        theme: state.theme === themes.dark ? themes.light : themes.dark,
      }));
    };
  }

  render() {
    // The ThemedButton button inside the ThemeProvider
    // uses the theme from state while the one outside uses
    // the default dark theme
    return (
      <Page>
        <ThemeContext.Provider value={this.state.theme}>
          <Toolbar changeTheme={this.toggleTheme} />
        </ThemeContext.Provider>
        <Section>
          <ThemedButton />
        </Section>
      </Page>
    );
  }
}
```

##### 从嵌套组件中更新 Context

经常需要在组件树嵌套很深的组件中更新 context。这种情况你可以传递个函数给 context，允许消费组件去更新 context。
**theme-context.js**

```jsx
// Make sure the shape of the default value passed to
// createContext matches the shape that the consumers expect!
export const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {},
});
```

**theme-toggler-button.js**

```jsx
import { ThemeContext } from "./theme-context";

function ThemeTogglerButton() {
  // The Theme Toggler Button receives not only the theme
  // but also a toggleTheme function from the context
  return (
    <ThemeContext.Consumer>
      {({ theme, toggleTheme }) => (
        <button
          onClick={toggleTheme}
          style={{ backgroundColor: theme.background }}
        >
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}

export default ThemeTogglerButton;
```

**app.js**

```jsx
import { ThemeContext, themes } from "./theme-context";
import ThemeTogglerButton from "./theme-toggler-button";

class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggleTheme = () => {
      this.setState((state) => ({
        theme: state.theme === themes.dark ? themes.light : themes.dark,
      }));
    };

    // State also contains the updater function so it will
    // be passed down into the context provider
    this.state = {
      theme: themes.light,
      toggleTheme: this.toggleTheme,
    };
  }

  render() {
    // The entire state is passed to the provider
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    );
  }
}

function Content() {
  return (
    <div>
      <ThemeTogglerButton />
    </div>
  );
}

ReactDOM.render(<App />, document.root);
```

##### 消费多个 Contexts

为了让 context 重新渲染更快，React 需要让每个 context 消费者树中是一个单独的节点。

```jsx
// Theme context, default to light theme
const ThemeContext = React.createContext("light");

// Signed-in user context
const UserContext = React.createContext({
  name: "Guest",
});

class App extends React.Component {
  render() {
    const { signedInUser, theme } = this.props;

    // App component that provides initial context values
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}

// A component may consume multiple contexts
function Content() {
  return (
    <ThemeContext.Consumer>
      {(theme) => (
        <UserContext.Consumer>
          {(user) => <ProfilePage user={user} theme={theme} />}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```

如果你需要一起使用多个 context 值，你可能想要考虑创建自己的 render prop 组件提供它们。

#### 注意事项

因为 context 使用引用来确认什么时候重新渲染，当 Provider 的父亲重新渲染时会触发一些意外的消费者渲染。举例，下面代码在每次 Provider 重新渲染都会触发所有消费者重新渲染，因为 value 一直创建的是一个新对象。

```jsx
class App extends React.Component {
  render() {
    return (
      <MyContext.Provider value={{ something: "something" }}>
        <Toolbar />
      </MyContext.Provider>
    );
  }
}
```

为了解决这个问题，提升值为父组件的状态：

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: { something: "something" },
    };
  }

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    );
  }
}
```

#### 旧版 API

> 注意：
> React 之前准备了一个实验性 context API。这个旧 API 将支持所有 16.x 版本，但是应用使用它应该迁移到新版本。这个旧版本 API 将在未来主要 React 版本中删除。React 的[旧 context 文档在这](https://reactjs.org/docs/legacy-context.html)

### Error Boundaries

> 在过去, 在组件内部的 JavaScript 通常会破坏 React 内部状态并在下一个渲染造成隐秘的错误。这些错误通常由早期的应用代码造成的，但是 React 没有提供在组件中逐渐的处理它们，并且无法从这种状态下恢复。

#### 介绍错误边界

UI 的部分 JavaScript 错误不应该破坏整个应用。为 React 用户解决这个问题，React16 介绍了一个新的概念“错误边界”。

错误边界是 React 组件，可以**捕获任何在它子组件树下发生的 JavaScript 错误，打印它们的日志，并显示一个备份 UI**替换崩溃的组件树。错误边界捕获在渲染期间，生命周期方法，以及整个树结构中的构造函数等的错误。

> 注意
> 错误边界不捕获这些错误

- 事件处理器([学习更多](https://reactjs.org/docs/error-boundaries.html#how-about-event-handlers))
- 异步代码(比如 setTimeout 或者 requestAnimationFrame 回调)
- 服务端渲染
- 错误边界自己抛出的错误（而不是它的子组件）

如果在 class 组件中定义了[static getDerivedStateFromError()](https://reactjs.org/docs/react-component.html#static-getderivedstatefromerror)或者[componentDidCatch()](https://reactjs.org/docs/react-component.html#componentdidcatch)生命周期函数，这个组件就会变成错误边界。使用 static getDerivedStateFromError()在一次抛出之后渲染备份 UI。使用 componentDidCatch()来打印错误信息。

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新状态所以下一个渲染将会渲染备份UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你可以将打印错误上报到服务器
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染备份UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

然后你可以将它作为一个常规的组件使用：

```jsx
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

错误边界像 JavaScript 的 catch{}块，但是用于组件。只有 class 组件可以作为错误边界。实践中，大多数情况你只想声明一次错误边界来捕获整个应用。

注意**错误边界只捕获这个组件树节点之下抛出的异常**。错误边界无法捕获自身的错误。如果错误边界在渲染错误消息的时候失败，这个错误会向上传播到最近的错误边界。这根在 JavaScript 的 catch 块工作一样。

#### Live Demo

检查[React 16](https://reactjs.org/blog/2017/09/26/react-v16.0.html)下[声明和使用错误边界的案例](https://codepen.io/gaearon/pen/wqvxGa?editors=0010)

#### 哪里放置错误边界

错误边界的粒度取决与你。你可以路由组件的顶层包装去给用户显示“遇到一些错误”，就像服务端框架经常遇到的崩溃。你可以将单个组件包装到错误边界内，不让它们崩溃应用程序的其余部分。

#### 未捕获错误的新行为

这个改动有一个重要的影响。**从 React16 开始，未被错误边界捕获的错误将造成整个 React 应用树卸载**

我们对这个决定做了辩论，但是根据我们的经验，将损坏的 UI 留着比完整删除它更糟糕。举例，在 Messenger 这样的产品保留损坏的 UI 显示会导致某人发送消息给错误的人。同样，对于付费应用来说显示错误的金额要比什么都不显示更糟糕。

这个改动意味着你迁移到 React16 之后，你将发现你应用中未覆盖之前已经存在的错误。添加错误边界让你当错误发生时为用户提供更加友好的体验。

例如，FaceBook Messenger 将侧边内容，信息面板，对华人之和消息输入包装到单独的错误边界中。如果这些 UI 组件之中发生崩溃，剩余的部分仍然保持交互。

我们还鼓励你使用 JS 错误上报服务，让你可以了解更多在生产中未处理的异常，然后修掉它们。

#### 组件堆栈跟踪

React 16 在开发环境下像控制台打印在渲染期间遇到的所有错误，即使应用意外的吞噬了它们。除了错误消息和 JavaScript 堆栈，它也提供组件堆栈的跟踪。现在你可以发现发生错误在组件树中确切的位置。
![](https://reactjs.org/static/f1276837b03821b43358d44c14072945/c3a47/error-boundaries-stack-trace.png)
你也可以看到在组件堆栈中文件名和确切的行号。默认情况下，在[Create React App](https://github.com/facebookincubator/create-react-app)项目中有效。
![](https://reactjs.org/static/45611d4fdbd152829b28ae2348d6dcba/6dd26/error-boundaries-stack-trace-line-numbers.png)
如果你没有使用 Create React App，你可以手动在你的 Babel 配置中添加[这个插件](https://www.npmjs.com/package/babel-plugin-transform-react-jsx-source)。注意它仅用于开发，必须在生产中禁止。

> 注意
> 堆栈中的组件命名显示依赖于[Function.name](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/name)属性。如果你支持还没有提供此特性的旧浏览器和设备(比如，IE11), 考虑在应用包中包含 Function.name 的 polyfill，像[function.name-polyfill](https://github.com/JamesMGreene/Function.name)。或者，你可以显示的在你所有组件上设置[displayName](https://reactjs.org/docs/react-component.html#displayname)。

#### 使用 try/catch 如何？

try/catch 很好，但是只针对命令式代码生效：

```jsx
try {
  showButton();
} catch (error) {
  // ...
}
```

但是，React 组件是声明式的，指定应该显示什么：

```jsx
<Button />
```

错误边界保留了 React 的声明特性，表现如您所愿。举例，即使由树深处某个地方 setState 引起了 componentDidUpdate 方法发生错误，它仍将正确的传播到最近的错误边界。

#### 在事件处理器中如何表现？

错误边界不会去捕获在事件处理器中的异常。

React 不需要错误边界去覆盖在事件处理器中的异常。不像渲染方法和生命周期，这个事件处理器不会发生在渲染期间。所以如果它们抛出，React 仍然知道屏幕上渲染什么。

如果你需要在事件处理器中捕获错误，使用常规的 JavaScript try/catch 语句：

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { error: null };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    try {
      // Do something that could throw
    } catch (error) {
      this.setState({ error });
    }
  }

  render() {
    if (this.state.error) {
      return <h1>Caught an error.</h1>;
    }
    return <button onClick={this.handleClick}>Click Me</button>;
  }
}
```

注意上面的案例演示了常规 JavaScript 的行为，没有使用错误边界。

#### 从 React 15 开始命名更改

React 15 用一个不同的名字 unstable_handleError 下对错误边界很有限。这个方法从 16 beta 版本开始就不在工作，你需要把它放到 componentDidCatch 中。

对于这个更改，我们提供了[codemod](https://github.com/reactjs/react-codemod#error-boundaries)来自动迁移你的代码。

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
