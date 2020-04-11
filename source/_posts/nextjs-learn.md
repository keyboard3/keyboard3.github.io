---
title: nextjs-learn
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 08:33:11
password:
summary:
tags: [next.js, 翻译, 进行中]
categories:
---

## 参考

原文 [Nextjs learn](https://nextjs.org/learn/basics/getting-started)

## 介绍

如今创建单页 js 应用可能的困难，已经不再是秘密。幸运的是，已经有多个框架和库可以供选择。
即使到现在，在你构建一个不错的应用之前，它仍然具有很高的学习曲线。因为你需要学习客户端路由，页面布局，api 调用等等。甚至，你想要服务端渲染核心页面并且其他页面静态预先渲染（为了平衡 SEO 和加载速度）
**所以，我们需要这些能够简单使用且能够自定义**
思考下，PHP 如何构建 web 应用。你只需要创建一些文件，写下一些 PHP 代码，然后简单的部署它。我们不必关心路由匹配，并且应用默认支持服务端渲染。
**Next.js**
这是我们用 next 所做的。我们用 js 和 React 构建 APP 而不是 PHP。这里有一些 NextJs 很酷的特性，如下

- 一个直观的基于页面的路由系统（且支持动态路由）
- 在可能的情况下自动静态优化页面
- 服务端渲染页面携带阻塞数据
- 自动代码分割让页面加载更快
- 支持客户端路由预加载页面
- 内置 CSS 支持，支持任何 css in js 的库
- 基于 webpack 支持开发环境热加载
- API 路由支持无服务函数和通用页面的简单路由来构建你的 API。
- 可以用社区插件和自己的 Babel 和 Webpack 配置进行自定义

### 安装

Next.js 可以用 Windows, Mac and 类 Linux 工作。你只需要在你的电脑上安装 Node.js，然后用它来开始构建 Next.js 应用
除此之外你还需要用文本编辑工具来写代码，以及用终端程序调用一些命令

> 如果你用 windows，尝试使用 PowerShell 或者 Git Bash（由 git 提供）。Next.js 可以用任何 shell 或者终端来使用，但是我们需要在这个文档中来使用 UNIX 的专门命令。我们建议使用 PowerShell 来跟着文档更加轻松一些。
> 开始，通过以下命令来创建一个应用

```
 mkdir hello-next
 cd hello-next
 npm init -y
 npm install --save react react-dom next
 mkdir pages
```

然后在 hello-next 目录，打开其中的 package.json 文件，用以下命令替换 Scripts

```
"scripts": {
  "dev": "next",
  "build": "next build",
  "start": "next start"
}
```

工作已经完成，运行下面命令启动 dev server

```
npm run dev
```

用`http://localhost:3000`来打开网站

### 创建我们第一个页面

现在让我们来创建第一个页面。创建`pages/index.js`文件，并将下面内容添加进去

```
export default function Index() {
  return (
    <div>
      <p>Hello Next.js</p>
    </div>
  );
}
```

现在如果你再次去访问，你将会看到`"Hello Next.js"`
这样，我们从`pages/index.js`导出一个简单的 React 元素组件。同样你可以写下自己的 React 元素并导出它。

```
保证你的React组件有default export
```

现在尝试在 Index 页面提示语法错误。案例（我们简单的移除了闭合的 p 标签）

### 处理错误

默认情况，Next.js 将会跟踪错误，然后将它显示到浏览器中。它帮助我们更快的识别和修复它们。
一旦修复，页面立刻更新，不需要整个页面重新加载。我们使用 webpack 的[hot module replacement](https://webpack.js.org/concepts/hot-module-replacement/)的设施来支持它，Next.js 默认支持 webpack 热加载。

## 在页面间导航

现在我们知道如何创建一个简单的 Next.js 应用并且运行它了。我们的简单应用只有一个页面，但是我们可以添加我们想要的页面。举个例子，我可以创建 About 页面，将下面的内容添加`pages/about.js`文件中。

```
export default function About() {
  return (
    <div>
      <p>This is the about page</p>
    </div>
  );
}
```

然后我们可以用`http://localhost:3000/about` 来访问到它了。
之后，我们需要将这些页面连接起来。我们可以用 a 标签做到。然而，它不会执行客户端导航；而是浏览器会直接请求服务器获取对应页面，刷新整个页面。这不是我们想要的。
为了支持 Next.js 的导航，我们需要使用 Link 组件，它由`next/link`导出。使用它导航，页面不会刷新

### 安装

为了跟下这个课程，你需要有简单的 Next.js 应用。为此，请继续你上一节课的工作或者下载整个示例应用

```
git clone https://github.com/zeit/next-learn-demo.git
cd next-learn-demo
cd 1-navigate-between-pages
```

你可以用下面运行

```
npm install
npm run dev
```

### Using Link

### Client-Side History Support

### Adding Link Props

### Link is Just a Wrapper Component

### Link is Simple, but Powerful

## Using Shared Components

## Create Dynamic Pages

## Clean URLs with Dynamic Routing

## 页面获取数据

### 介绍

现在我们知道如何创建一个不错的 Next.js 应用，并且能够充分利用 Next.js 的路由 API 的优势。
实践中，我们通常需要从远程数据源中获取数据。Next.js 有标准的 API 为页面获取数据。我们使用`getInitialProps`异步函数来做到。

> `getInitialProps`只允许添加的组件，作为 Page 使用。添加到其他组件中不会工作。

使用`getInitialProps`，我们给指定页面通过远程数据源获取数据，并将数据作为属性传入我们的页面中。`getInitialProps`需要能够在服务端和客户端都能够运行；因为它在两个环境中都被调用。
在这个课程中，使用`getInitialProps`，我们将利用公开的[TVmaze API](https://www.tvmaze.com/api)来创建一个 app 用来显示蝙蝠侠电视剧的信息。

### 安装
在这个课程中，我们需要简单的应用运行。试着下载下面的示例而应用：
```
git clone https://github.com/zeit/next-learn-demo.git
cd next-learn-demo
cd 6-fetching-data
```
运行使用下面：
```
npm install
npm run dev
```
### 获取蝙蝠侠电视信息
在我们的应用中，首页有一个博客列表。现在我们将在列表上显示蝙蝠侠的信息。而不是写死这些信息，我们需要从远端服务器获取它们。
> 这里我们需要使用[TVmaze API](https://www.tvmaze.com/api)来获取这些信息。这个api用来搜索电视剧信息

首先我们需要安装[isomorphic-unfetch](https://github.com/developit/unfetch)。这个库我们会用来获取数据。它是浏览器`fetch`API的简单实现，但是它在客户端和服务端环境下都能够运行。
```
npm install --save isomorphic-unfetch
```
用下面内容替换`pages/index.js`
```
import Layout from '../components/MyLayout';
import Link from 'next/link';
import fetch from 'isomorphic-unfetch';

const Index = props => (
  <Layout>
    <h1>Batman TV Shows</h1>
    <ul>
      {props.shows.map(show => (
        <li key={show.id}>
          <Link href="/p/[id]" as={`/p/${show.id}`}>
            <a>{show.name}</a>
          </Link>
        </li>
      ))}
    </ul>
  </Layout>
);

Index.getInitialProps = async function() {
  const res = await fetch('https://api.tvmaze.com/search/shows?q=batman');
  const data = await res.json();

  console.log(`Show data fetched. Count: ${data.length}`);

  return {
    shows: data.map(entry => entry.show)
  };
};

export default Index;
```
这个静态异步函数你可以添加到你应用的任何页面中去。使用它，我们可以获取数据，将它作为属性传递给页面
如你所见，现在我们获取蝙蝠侠的数据，然后将他们传递给页面显示出来
`这里假装有张图`
---
这个异步函数会在控制台下打印数据的舒朗。现在，可以去看服务端和客户端的控制台。然后刷新页面。猜测打印的结果
结果是只在服务端打印。因为已经在服务端请求过数据，不会再在客户端请求
### 实现博客页面
现在添加详情页面显示详细数据
用下面的内容替换到`pages/p/[id].js`
```
mport Layout from '../../components/MyLayout';
import fetch from 'isomorphic-unfetch';

const Post = props => (
  <Layout>
    <h1>{props.show.name}</h1>
    <p>{props.show.summary.replace(/<[/]?[pb]>/g, '')}</p>
    {props.show.image ? <img src={props.show.image.medium} /> : null}
  </Layout>
);

Post.getInitialProps = async function(context) {
  const { id } = context.query;
  const res = await fetch(`https://api.tvmaze.com/shows/${id}`);
  const show = await res.json();

  console.log(`Fetched show: ${show.name}`);

  return { show };
};

export default Post;
```
函数的第一个参数是context上下文对象。它包含`query`对象来获取实际请求参数
在我们的案例中，我们从`query`中获取id，然后用它来从api中获取详情数据。
跳转到详情页，数据只会在客户端加载。
### 最后
现在呢学习到了Next.js的核心特性之一，如何获取数据以及服务端渲染。
我们学习了`getInitialProps`基础概念以及一些它的案列。你可以参考文档获取更多的信息。
## Styling Components

## API Routes

## Deploying a Next.js App

# Export into a Static HTML App

# TypeScript

# Create AMP Pages

# Automatic Static Optimization
