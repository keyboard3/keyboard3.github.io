---
title: nextjs-learn
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 08:33:11
password:
summary:
tags: [next.js, 翻译,未完待续]
categories:
---
## 参考
原文 [Nextjs learn](https://nextjs.org/learn/basics/getting-started)
## 介绍
如今创建单页js应用可能的困难，已经不再是秘密。幸运的是，已经有多个框架和库可以供选择。
即使到现在，在你构建一个不错的应用之前，它仍然具有很高的学习曲线。因为你需要学习客户端路由，页面布局，api调用等等。甚至，你想要服务端渲染核心页面并且其他页面静态预先渲染（为了平衡SEO和加载速度）
**所以，我们需要这些能够简单使用且能够自定义**
思考下，PHP如何构建web应用。你只需要创建一些文件，写下一些PHP代码，然后简单的部署它。我们不必关心路由匹配，并且应用默认支持服务端渲染。
**Next.js**
这是我们用next所做的。我们用js和React构建APP而不是PHP。这里有一些NextJs很酷的特性，如下
 - 一个直观的基于页面的路由系统（且支持动态路由）
 - 在可能的情况下自动静态优化页面
 - 服务端渲染页面携带阻塞数据
 - 自动代码分割让页面加载更快
 - 支持客户端路由预加载页面
 - 内置CSS支持，支持任何css in js的库
 - 基于webpack支持开发环境热加载
 - API路由支持无服务函数和通用页面的简单路由来构建你的API。
 - 可以用社区插件和自己的Babel和Webpack配置进行自定义
 ### 安装
 Next.js 可以用 Windows, Mac and 类Linux工作。你只需要在你的电脑上安装Node.js，然后用它来开始构建Next.js应用
 除此之外你还需要用文本编辑工具来写代码，以及用终端程序调用一些命令
 > 如果你用windows，尝试使用PowerShell或者Git Bash（由git提供）。Next.js可以用任何shell或者终端来使用，但是我们需要在这个文档中来使用UNIX的专门命令。我们建议使用PowerShell来跟着文档更加轻松一些。
 开始，通过以下命令来创建一个应用
 ```
  mkdir hello-next
  cd hello-next
  npm init -y
  npm install --save react react-dom next
  mkdir pages
  ```
然后在hello-next目录，打开其中的package.json文件，用以下命令替换Scripts
```
"scripts": {
  "dev": "next",
  "build": "next build",
  "start": "next start"
}
```
工作已经完成，运行下面命令启动dev server
```
npm run dev
```
用http://localhost:3000来打开网站
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
这样，我们从`pages/index.js`导出一个简单的React元素组件。同样你可以写下自己的React元素并导出它。
```
保证你的React组件有default export
```
现在尝试在Index页面提示语法错误。案例（我们简单的移除了闭合的p标签）
### 处理错误
默认情况，Next.js将会跟踪错误，然后将它显示到浏览器中。它帮助我们更快的识别和修复它们。
一旦修复，页面立刻更新，不需要整个页面重新加载。我们使用webpack的[hot module replacement](https://webpack.js.org/concepts/hot-module-replacement/)的设施来支持它，Next.js默认支持webpack热加载。
## 在页面间导航
现在我们知道如何创建一个简单的Next.js应用并且运行它了。我们的简单应用只有一个页面，但是我们可以添加我们想要的页面。举个例子，我可以创建About页面，将下面的内容添加`pages/about.js`文件中。
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
之后，我们需要将这些页面连接起来。我们可以用a标签做到。然而，它不会执行客户端导航；而是浏览器会直接请求服务器获取对应页面，刷新整个页面。这不是我们想要的。
为了支持Next.js的导航，我们需要使用Link组件，它由`next/link`导出。使用它导航，页面不会刷新
### 安装
为了跟下这个课程，你需要有简单的Next.js应用。为此，请继续你上一节课的工作或者下载整个示例应用
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
## Fetching Data for Pages
## Styling Components
## API Routes
## Deploying a Next.js App
# Export into a Static HTML App
# TypeScript
# Create AMP Pages
# Automatic Static Optimization