---
title: next.js docs
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 10:47:26
password:
summary:
tags: [nextjs, 翻译, 进行中]
categories:
---

[nextjs-docs](https://nextjs.org/docs/getting-started)

# 文档

## 介绍

如果你是新人，建议你学习改[课程](https://nextjs.org/learn/basics/getting-started)
带有测试的交互课程会指导你完成使用 Next.js 所需要的知识
如果你有任何有关 Next.js 的问题，你可以去[社区](https://github.com/zeit/next.js/discussions)中提问
**系统要求**

- Node.js 10 以上
- MacOS，windows(包括 WSL),以及 Linux

### 设置

我们建议使用 create-next-app 来创建 Next.js 应用，它将自动为你设置一切。运行以下命令创建项目：

```
npm init next-app
# or
yarn create next-app
```

安装完成之后，跟着说明启动开发服务。尝试修改 pages/index.js 并在你浏览器上查看结果。

### 手动设置

在你的项目中安装 next,react 和 react-dom：

```
npm install next react react-dom
```

打开 package.json 并添加下面的 scripts

```js
"scripts": {
  "dev": "next",
  "build": "next build",
  "start": "next start"
}
```

下面脚本指向开发应用的不同阶段：

- dev - 运行 next 在开发模式下启动 Next.js
- build - 运行 next build 为生产模式构建应用
- start - 运行 next start 启动 Next.js 生产服务

Next.js 是围绕页面概念构建的。 页面是从在 pages 目录下的.js,.jsx,.ts 或者.tsx 文件中导出的 react 组件

页面是基于它们的文件名来关联路由的。例如 pages/about.js 被映射成 /about. 你甚至可以在文件名上添加动态路由参数。

在你的项目中创建 pages 目录

使用下面的内容填充到 ./pages/index.js

```jsx
function HomePage() {
  return <div>Welcome to Next.js!</div>;
}

export default HomePage;
```

运行 npm run dev 来开始开发应用。它启动一个开发服务在 http://localhost:3000

通过 http://localhost:3000 来查看你的应用

到此为止，我们获得：

- 自动编译和打包 （通过 webpack 和 babel）
- 热加载
- 静态生成和服务端渲染 ./pages/
- 静态文件服务 ./public/ 映射成 /

## 基础功能

### Pages

> 这个文档基于 Next.js 9.3 及以上，如果你使用的是旧版，参考 [之前的文档](https://nextjs.org/docs/tag/v9.2.2/basic-features/pages)

在 Next.js 中，一个页面就是一个组件，它们从 pages 目录文件下的 .js, jsx, .ts 或者 .tsx 文件中导出。每个页面基于它们的文件名与路由关联。

例如：如果你创建 pages/about.js，并导出下面的 React 组件，它将可以通过 /about 访问。

```jsx
function About() {
  return <div>About</div>;
}

export default About;
```

#### Pages 使用动态路由

Next.js 支持页面使用动态路由。比如，如果你创建文件名叫做 pages/posts/[id].js, 然后它就可以通过 posts/1, posts/2,等等。

> 学学习更多动态路由知识，查看[动态路由文档](https://nextjs.org/docs/routing/dynamic-routes)

#### 预渲染

默认，Next.js 对每个页面预渲染。这意味着 Next.js 会提前为每个页面生成 HTML，而不是由客户端 JavaScript 来生成。预渲染会带来更好的性能和 SEO。

每个生成的 HTML 只关联那个页面需要的最少 js 代码。当页面被浏览器加载完毕，它的 js 代码运行会使得页面完全可交互。（这个过程被叫做水合/hydration）

##### 两种预渲染的方式

Next.js 有两种预渲染方式：静态生成和服务端渲染。这区别在于为页面生成 HTML 的时机。

- **静态生成（建议）**：HTML 在构建时被生成，并可以被每个请求复用。
- **服务端渲染**：HTML 在每次请求时都会生成

重要的是，Next.js 让你可以选择你每个页面的预渲染方式。你可以针对大部分页面使用静态生成方式以及针对特殊请求使用服务端渲染来，创建一个“混合” Next.js 应用。

我们建议使用静态生成来代替服务端渲染来提高性能。静态生成的页面可以被 CDN 缓存来提高启动性能。然而，在某些情况下，服务端渲染可能是唯一的选择。

最后，你可以始终将客户端渲染和静态生成或者服务端渲染一起使用。这意味着页面的某些部分可以完全的由客户端代码渲染。为了进一步学习，可以查看[数据获取](https://nextjs.org/docs/basic-features/data-fetching#fetching-data-on-the-client-side)文档

#### 静态生成（建议）

如果页面使用静态生成，页面的 HTML 将在构建时被生成。这意味着，在生产环境下，页面 HTML 在你运行 next build 时生成。这个 HTML 将会被每个请求复用，它也可以被 CDN 缓存。

在 Next.js 中，你可以带着数据或者不带数据生成页面，让我们来看看这两个情况。

##### 不带数据静态生成

默认情况下，Next.js 不带数据静态生成预渲染页面。下面是案例：

```jsx
function About() {
  return <div>About</div>;
}

export default About;
```

注意这个页面不需要获取任何额外数据去预渲染。这个场景比如：Next.js 在构建时对每个 page 生成单个 HTML。

##### 带数据静态生成

有些页面在预渲染时获取额外数据。这里有两个情况，一种或者两种都适用。每种情况，那你可以适用 Next.js 提供的特殊功能:

1. 页面内容依赖外部数据：适用 getStaticProps
2. 页面路径依赖外部数据：适用 getStaticPaths (通常也用到 getStaticProps)

###### 场景 1：页面内容依赖额外数据

例如：你博客页面可能需要从 CMS（内容管理系统）中获取博客列表。

```jsx
// TODO: 需要在页面预渲染之前通过调用 api 获取 posts
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

export default Blog;
```

为了在预渲染时获取这个数据，Next.js 允许在这个文件下导出 getStaticProps 的异步函数。这个函数在构建时调用，然后在预渲染时将获取的数据传递给页面的 props。

```jsx
// 你可以使用任何数据获取库
import fetch from "node-fetch";

function Blog({ posts }) {
  // 渲染 posts...
}

// 这个函数在构建时被调用
export async function getStaticProps() {
  // 调用外部api获得posts
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // 返回 { props: posts }, 这个 Blog component
  // 在构建时将接收 `posts` 作为属性
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

为了学习 getStaticProps 如何工作，查看[数据获取文档](https://nextjs.org/docs/basic-features/data-fetching#getstaticprops-static-generation)

###### 场景 2：页面路径依赖额外数据

Next.js 允许你通过动态路由来创建页面。比如，你可以创建 pages/posts/[id].js 来基于 id 来显示单个博客。当你访问 posts/1 是博客将获得 id:1 的参数来显示博客内容。

> 为了进一步学习动态路由，查看[动态路由文档](https://nextjs.org/docs/routing/dynamic-routes)

然而，你可能需要额外的数据提供 id 来预渲染。
例如：假设你只在数据库中添加了 id:1 的文章。这种情况下，你值需要在构建时渲染 posts/1。

随后，你可能添加了 id:2 的文章，然后你想要同样的预渲染 posts/2。

所以你预渲染的页面路径需要依赖外部数据。为了处理它，Next.js 让你在动态页面( pages/posts/[id].js )中导出异步函数 getStaticPaths。这个函数在构建时调用，并让你可以指定那些路径你想要预渲染。

```js
import fetch from "node-fetch";

// 在构建时被调用
export async function getStaticPaths() {
  // 调用外部api获取文章列表
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // 获取我们想要渲染的文章路径
  const paths = posts.map((post) => `/posts/${post.id}`);

  // 我们只在构建时预渲染这些路径
  // { fallback: false } 因为着其他路径将是404
  return { paths, fallback: false };
}
```

同样在 pages/posts/[id].js 中，你需要导出 getStaticProps 来根据 id 获取数据预渲染页面。

```jsx
import fetch from "node-fetch";

function Post({ post }) {
  // Render post...
}

export async function getStaticPaths() {
  // ...
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // Pass post data to the page via props
  return { props: { post } };
}

export default Post;
```

##### 什么时候使用静态生成？

我们建议尽可能的使用静态生成，因为那你的页面只被构建一次且可以被 CDN 缓存，可以使得比每次请求都服务端渲染快很多。

许多页面类型都可以使用静态生成，包括：

- 营销页面
- 博客文章
- 电子商务产品列表
- 帮助和文档

你应该问你自己：“我们可以在用户请求之前预渲染页面吗？” 如果答案是 yes，然后你应该选择静态生成。

另一方面，如果在用户请求之前无法预渲染，静态生成则不是一个好建议。可能你的页面频繁根据数据刷新，页面内容在每次请求时改变。

这种情况，你可以渲染以下任何操作。

- 使用带客户端渲染的静态生成：你可以跳过页面的某些部分预渲染，然后使用客户端渲染来填充它们。
- 使用服务端渲染：Next.js 在每次请求预渲染。它会很慢，因为页面无法被 CDN 缓存，但是预渲染页面是实时刷新的。

#### 服务端渲染

> 也叫做 SSR 或者动态渲染

如果页面使用服务端渲染，页面 HTML 每次请求时生成。

为了让页面使用服务端渲染，你需要导出 getServerSideProps 异步函数。这个函数将在每次请求时在服务端被调用。

例如，假设你的页面需要用最新的数据预渲染（通过外部的 api 获取数据）。你应该写下 getServerSideProps 来获取数据传递给 Page。

```js
import fetch from "node-fetch";

function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // Pass data to the page via props
  return { props: { data } };
}

export default Page;
```

如你所见，getServerSideProps 和 getStaticProps 很像，但是区别的是，getServerSideProps 是每个请求都会调用而不是在构建时。

#### 总结

我们讨论了 Next.js 中的两种预渲染方式。

- 静态生成（推荐）：页面在构建时生成并每次请求都可以复用。为了让页面使用静态生成，导出纯页面组件，或者导出 getStaticProps（如果需要导出 getStaticPaths）。会在用户请求之前就预渲染好。你也可以使用客户端渲染额外的数据。

- 服务端渲染：每次请求都会生成 HTML。为了让页面使用服务端渲染，导出 getServerSideProps。因为服务端渲染的结果性能比静态生成差，只有当绝对必须的时候才用它。

### 数据获取

在 Pages 文档中，我们解释了 Next.js 有两种预渲染方式：静态生成和服务端渲染。在这个页面，我们将深度讨论每种情况的数据获取策略。我们建议你首先读一下 Pages 文档。

我们将讨论 3 种你可以在预渲染时使用的 Next.js 函数：

- getStaticProps(静态生成)：在构建时获取数据
- getStaticPaths(静态生成)：指定预渲染时的根据数据的动态路由
- getServerSideProps(服务端渲染)：在每次请求时获取数据。

另外，我们将简要讨论如何在客户端获取数据。

#### getStaticProps(静态生成)

如果你在页面中导出 getStaticProps 异步函数，Next.js 将在构建时使用 getStaticProps 返回的数据作为 Props 渲染页面。

```js
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  };
}
```

这个 Context 参数是一个包含下面属性的对象：

- params 包含了当页面使用动态路由的路由参数。例如，如果页面名是 [id].js ，然后 params 将是 {id:...}。会和 getStaticPaths 一起使用
- preview 是 true 表示页面是在预览模式下。[预览模式文档](https://nextjs.org/docs/advanced-features/preview-mode)
- previewData 包含通过 setPreviewData 设置的预览数据。[预览模式文档](https://nextjs.org/docs/advanced-features/preview-mode)

##### 简单实例

下面的案例使用 getStaticProps 从 CMS 中获取博客章节列表。

```jsx
// You can use any data fetching library
import fetch from "node-fetch";

// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries. See the "Technical details" section.
export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // By returning { props: posts }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

##### 什么时机应该使用 getStaticProps?

当遇到以下条件时应该使用 getStaticProps:

- 在用户请求之前的构建阶段渲染有数据的页面是有效的
- 数据来自无头的 CMS
- 数据可以被公开缓存（不带用户信息）
- 页面需要预渲染（为 SEO）并且非常快 - getStaticProps 生成 HTML 和 JSON 文件，它们可以被 CDN 缓存来提高性能

##### TypeScript: 使用 GetStaticProps

对于 TypeScript,你可以从 next 中使用 GetStaticProps 类型：

```js
import { GetStaticProps } from "next";

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
};
```

##### 读文件：使用 process.cwd()

在 getStaticProps 中可以从文件系统中被直接读取文件。

为了做到，你需要获取文件的全路径。

因为 Next.js 编译你的代码到独立的目录，你无法使用 `__dirname` 作为路径，因为它返回的不是 pages 目录。

你可以使用 process.cwd()，它会给你 Next.js 被执行的目录。

```jsx
import fs from "fs";
import path from "path";

// 文章列表将在构建时被getStaticProps()填充
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  );
}

// 这个函数将在服务端构建时被调用
// 它不会被在客户端调用，所以你甚至可以直接访问数据库
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), "posts");
  const filenames = fs.readdirSync(postsDirectory);

  const posts = filenames.map((filename) => {
    const filePath = path.join(postsDirectory, filename);
    const fileContents = fs.readFileSync(filePath, "utf8");

    // 通常你应该解析和转换内容
    // 比如你可以在这里转换markdown成HTML

    return {
      filename,
      content: fileContents,
    };
  });
  // 返回 { props: posts }, the Blog component 将在构建时接收posts作为属性
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

#### 技术细节

**只在构建时运行**
因为 getStaticProps 在构建时生成静态 HTML，所以它不会接收那些仅在请求时有效的数据，比如查询参数或者 HTTP 请求头。

**直接写服务端代码**
注意 getStaticProps 只运行在服务端。它永远不会运行到客户端。它不会包含为浏览器生成的 JS 打包文件。这因为着你可以写直接访问数据库查询的代码，不会发送到浏览器上。你可以不在 getStaticProps 调用 API，你可以直接在 getStaticProps 中写下服务端代码。

**静态生成 HTML 和 JSON**
当页面在构建时使用 getStaticProps 进行预渲染，除了页面内的 HTML 文件，Next.js 生成 JSON 文件来存储 getStaticProps 的运行结果。

这个 JSON 文件将使用在客户端通过`next/link`((文档)[https://nextjs.org/docs/api-reference/next/link])或者`next/router`((文档)[https://nextjs.org/docs/api-reference/next/router])导航时请求。当你导航到这个页面，它使用 getStaticProps，Next.js 获取这个 JSON 文件（构建时提前计算的），然后使用它作为页面组件的属性参数。这意味着客户端跳转时不会调用 getStaticProps，只使用导出的 JSON 文件。

**仅在页面中允许**
只能在 page 中导出 getStaticProps。不能再非 Page 文件下到导出。

其中一个限制原因是 React 需要在渲染页面之前加载所有的数据

同样，你必须使用`export async function getStaticProps() {}` - 如果你添加 getStaticProps 作为页面组件的属性，则无法工作。

**在开发模式下每个请求运行**
在开发模式下(`next dev`), getStaticProps 将在每次请求时调用。

**预览模式**
在某些情况下，你可能想要临时绕过静态生成，并在使用请求时渲染页面而不是使用构建时的结果。比如，你可能使用无头的 CMS 并想要在发布之前预览草稿。

这种场景 Next.j 使用预览模式的功能提供支持。进步了解[预览模式文档](https://nextjs.org/docs/advanced-features/preview-mode)

#### getStaticPaths (静态生成)

如果页面有动态路由（(文档)[https://nextjs.org/docs/routing/dynamic-routes]）并使用了 getStaticProps，需要定义 paths 列表，才会在构建时生成 HTML。

如果你在动态路由的 Page 中导出 getStaticPaths 异步函数，Next.js 将静态预渲染所有被 getStaticPaths 指定的路径。

```js
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true or false // See the "fallback" section below
  };
}
```

**paths 键必须**
这个 paths 键决定哪些路径会被预渲染。例如，假设你有一个命名为 pages/posts/[id].js 的动态路由页面。如果你在这个页面中导出 getStaticProps 并返回以下的路径：

```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```

然后 Next.js 将在构建时使用`pages/posts/[id].js`的页面组件静态生成`posts/1`和`posts/2`

注意每个 params 的值都必须与页面名称中使用的参数匹配：

- 如果页面名称是`pages/posts/[postId]/[commentId]`,params 应该包含 postId 和 commentId。
- 如果页面名使用捕获所有的路由，比如`pages/[...slug]`，然后 params 应该包含 slug 是个数组。比如，如果数组是`['foo','bar']`，然后 Next.js 将静态生成页面在`/foo/bar`

**fallback 键必须**
getStaticPaths 返回的对象一定要包含 bool 类型的 fallback 键。

`fallback:false`

如果 fallback 是 false，任何不在 getStaticPaths 的路径的结果将是 404 页面。如果你预渲染的路径数量很小的话可以之前在构建时全部生成。当新页面不经常添加时非常有用。如果你要向数据中添加很多数据项并需要渲染它们，你需要再次构建。

这里有个实例，每个预渲染的博客文章的页面都是`pages/posts/[id].js`。博客文章的列表在 getStaticPaths 中通过 CMS 获得。然后，每个页面都适用 getStaticProps 从 CMS 中获取文章数据。

```js
// pages/posts/[id].js
import fetch from "node-fetch";

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false };
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // Pass post data to the page via props
  return { props: { post } };
}

export default Post;
```

`fallback:true`
如果 fallback 是 true，getStaticProps 的行为将会发生变化：

- getStaticPaths 返回的路径将在构建时被渲染成 HTML
- 没有在构建时生成的路径将不会返回 404 页面。相反，Next.js 将在页面被第一次请求时提供后备版本。在下面看[后备页面](https://nextjs.org/docs/basic-features/data-fetching#fallback-pages)。
- 在后台，Next.js 将静态生成请求路径的 HTML 和 JSON。它们会运行 getStaticProps。
- 当这些完成，浏览器会接收到已生成的路径的 JSON 文件。然后将使用它作为参数自动渲染页面。从用户的视角看，页面从备份页面切换到了完整页面。
- 同时，Next.js 会将这个路径添加到预渲染页面列表。后续请求到相同路径将返回已生成的页面，就像其他在构建时预渲染页面一样。

**备份页面**
页面的备份版本：

- 页面的属性将是空的
- 使用 router,你可以检测如果 fallback 已经被渲染，router.isFallback 将是 true

下面是使用 isFallback 的案例：

```jsx
// pages/posts/[id].js
import { useRouter } from "next/router";
import fetch from "node-fetch";

function Post({ post }) {
  const router = useRouter();

  // 如果页面还没有被生成，它将显示
  // 初始化 until getStaticProps() 结束运行
  if (router.isFallback) {
    return <div>Loading...</div>;
  }

  // Render post...
}

// 这个函数将在构建时运行
export async function getStaticPaths() {
  return {
    // 只有 `/posts/1` and `/posts/2` 在构建时被运行
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    // 启用静态生成其他页面
    // 例如: `/posts/3`
    fallback: true,
  };
}

// 在构建时被执行
export async function getStaticProps({ params }) {
  // 参数包含文章 `id`.
  // 如果路由是 /posts/1, 这个 params.id 将是 1
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  // 传递文章数据作为页面属性
  return { props: { post } };
}

export default Post;
```

**什么时候`fallback:true`有用？**
`fallback:true`当你的应用有非常大数量的静态页面时有用（思考：大型电商网站）。你想要预渲染所有产品页面，但是你的构建将花费很长。

相反，你应该静态生成少量集合的页面，剩下的页面使用`fallback:true`。当有人请求没有生成过的页面时，用户将会看到加载进度条。很快，getStaticProps 完成页面将使用请求的数据渲染。那么从现在开始，所有请求相同路径的人将获得静态预渲染页面。

它保证在保障构建速度和静态生成的优势同时，用户也一直有很快的体验。

**什么时候应该使用 getStaticPaths?**
如果你使用动态路由来静态预渲染页面，那你应该使用 getStaticPaths

**TypeScript: 使用 GetStaticPaths**
在 TypeScript 中，你可以从 next 中使用 GetStaticPaths:

```js
import { GetStaticPaths } from "next";

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
};
```

##### 技术细节

**和 getStaticProps 一起使用**
当你在动态路由参数的页面上使用 getStaticProps 时，你肯定要使用 getStaticProps

你无法让 getStaticPaths 和 getServerSideProps 一起使用。

**只在服务端构建时运行**
getStaticPaths 只在服务端构建时运行

**只运行在 Page 中**
getStaticPaths 只能被 page 导出。你不能在非 Page 的文件中导出它

同样，你必须使用`export async function getStaticPaths() {}` -如果你添加 getStaticPaths 到页面组件上不生效

**在开发模式下每次请求运行**
在开发模式下（`next dev`）, getStaticPaths 将在每次请求时调用。

#### getServerSideProps(服务端渲染)

如果你在页面导出 getServerSideProps 异步函数，Next.js 将在每次请求时通过调用 getServerSideProps 来预渲染页面。

```js
export async function getServerSideProps(context) {
  return {
    props: {}, // 将作为页面组件的属性
  };
}
```

context 参数是包含下列 key 的对象：

- params: 如果页面使用动态路由，params 包含路由的参数。如果页面名字是`[id].js`，那么 params 将是这样的`{id:...}`
- req: Http IncomingMessage 对象.
- res: Http 响应对象
- query: 查询参数
- preview: true 如果页面是预览模式。否则就是 false
- previewData: 通过 setPreviewData 来设置预览数据

##### 简单案例

这里是使用 getSeverSideProps 在每次请求时获取数据预渲染的案例。

```jsx
function Page({ data }) {
  // Render data...
}

// 每次请求获取
export async function getServerSideProps() {
  // 通过api获取数据
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // 传递data作为页面属性
  return { props: { data } };
}

export default Page;
```

##### 什么时候应该使用 getServerSideProps?

只有当你预渲染的页面需要实时获取数据的时候才应该使用 getServerSideProps。相较于 getStaticProps 第一个字节到达(TTFB)的时间比较慢，因为服务端一定每次请求都要计算结果，并且这个结果没有额外的配置无法被 CDN 缓存。

如果你不需要预渲染数据，你可以考虑在客户端获取数据

##### TypeScript:使用 GetServerSideProps

对于 TypeScript， 你从 next 中使用 GetServerSideProps 类型

```js
import { GetServerSideProps } from "next";

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
};
```

#### 技术细节

**只在服务端运行**
getServerSideProps 只能运行服务端并且不会运行在浏览器上。如果页面使用 getServerSideProps，需要注意：

- 当你直接请求这个页面，getServerSideProps 会在每次请求时运行，并且页面每次预渲染返回 props。
- 当你在客户端通过`next/link`或者`next/router`来跳转时，Next.js 执行 getServerSideProps 发送 Api 请求给服务器。它将会返回 getServerSideProps 运行的结果，并且这个 JSON 将会被用来渲染页面。所有这些工作将被 Next.js 自动处理，所以除了 getServerSideProps 你不需要其他额外的定义。

**在能在 page 中导出**
getServerSideProps 只能被 page 导出。你无法在非 page 文件中导出。

同样，你必须使用`export async function getServerSideProps() {}` - 如果你将它作为页面组件的属性这无法工作

#### 在客户端请求数据

如果你页面需要频繁请求数据，你不需要预渲染数据，你可以只在客户端请求数据。在客户端请求数据的案例，如何工作：

- 首先，不带数据立刻显示页面。页面部分内容可以使用静态生成预渲染。你可以在缺失数据的请求下显示加载状态。
- 然后，在客户端请求数据，并在准备好时显示。

这种方式对于用户管理页面非常好，比如。因为管理面板是私有的，用户特有页面，不需要 SEO 则页面不需要预渲染。数据是频繁更新的，要求每次数据都实时请求。

#### SWR

Next.js 背后的团队创建[SWR](https://swr.now.sh/)来获取数据的 React Hook。我们强烈建议如果在客户端请求使用它。它会处理缓存，重新验证，焦点跟踪，间隔重新获取等等。你可以这么使用：

```js
import useSWR from "swr";

function Profile() {
  const { data, error } = useSWR("/api/user", fetch);

  if (error) return <div>failed to load</div>;
  if (!data) return <div>loading...</div>;
  return <div>hello {data.name}!</div>;
}
```

[进一步查看 SWR 文档](https://swr.now.sh/)

## 路由

## API 路由

Next.js 的 API routes 中提供了直接解决方案来构建你自己的 API
任何放到`pages/api`文件夹的文件会被映射成`/api/*`，会被认为是 API 还不是页面
比如，下面 API 路由`pages/api/user.js`处理一个简单的 json 响应：

```
export default (req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'application/json')
  res.end(JSON.stringify({ name: 'John Doe' }))
}
```

为了 API 路由正常工作，你需要导出一个 default 函数(请求处理函数),它会接收下面的参数

- `req`: 它是[http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)实例，加一些预先构建的中间件，你可以看[这里](https://nextjs.org/docs/api-routes/api-middlewares)
- `res`: 它是[http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)实例，添加一些帮助函数可以看[这里](https://nextjs.org/docs/api-routes/response-helpers)
  为了在一个 API 路由中处理不同的 http 方法，你可以使用`req.method`,比如

```
export default (req, res) => {
  if (req.method === 'POST') {
    // Process a POST request
  } else {
    // Handle any other HTTP method
  }
}
```

获取 API 节点，看下下面章节的案例

> API 路由不要求指定跨域头，意味着默认只支持同源请求。你可以用[跨域中间件](https://nextjs.org/docs/api-routes/api-middlewares#connectexpress-middleware-support)包装请求处理函数来自定义这个行为
> API 路由不会增加你客户端的文件大小，只会增加服务端的大小

### 动态 API 路由

API 路由支持动态路由，命名规则还是使用`pages`。例子，`pages/api/post/[pid].js`下面代码：

```
export default (req, res) => {
  const {
    query: { pid },
  } = req

  res.end(`Post: ${pid}`)
}
```

现在，请求`/api/post/abc`将会返回`Post: abc`

#### 捕获所有的路由

API 路由通过在括号中添加...捕获它下面所有路径，比如：

- `pages/api/post/[...slug].js`匹配`/api/post/a`,同样`/api/post/a/b`以及`/api/post/a/b/c`也会匹配

> 注意：你可以不用`slug`用其他的比如`[..param]`
> 匹配参数将会作为查询参数发送给页面（这个例子是`slug`）,它经常是一个数组，路径`/api/post/a`的`query`对象为：

```
{ "slug": ["a"] }
```

`/api/post/a/b`路径的参数这样

```
{ "slug": ["a", "b"] }
```

`pages/api/post/[...slug].js`的 API 处理可以这样：

```
export default (req, res) => {
  const {
    query: { slug },
  } = req

  res.end(`Post: ${slug.join(', ')}`)
}
```

现在，请求`/api/post/a/b/c`将返回`Post: a, b, c`

#### 注意事项

- 预先定义的 API 路由优先级高于动态路由，动态路由会捕获剩下的所有 API。看下下面的例子：
- `pages/api/post/create.js` - 将匹配 `/api/post/create`
- `pages/api/post/[pid].js` - 将匹配 `/api/post/1`, `/api/post/abc`, 等等. `/api/post/create`将不会被匹配
- `pages/api/post/[...slug].js` - 将匹配 `/api/post/1/2`, `/api/post/a/b/c`, 等等. `/api/post/create`, `/api/post/abc`将不会被匹配

### API 中间件

API 路由提供内置中间件解析进来的请求`req`。有这些中间件

- `req.cookies` - 对象包含请求发送过来的 cookie，默认是`{}`
- `req.query` - 对象包含[query string](https://en.wikipedia.org/wiki/Query_string)，默认是`{}`
- `req.body` - 对象包含根据`content-type`解析过的 body，如果没有`body`，它是 null

#### 自定义配置

每个 API 路由都可以到处一个`config`对象来修改默认配置，例如

```
export const config = {
  api: {
    bodyParser: {
      sizeLimit: '1mb',
    },
  },
}
```

这个`api`对象包含所有 API 路由的配置
`bodyParser`启用 body 解析，你如果想要自己消费`Stream`可以禁用掉
`bodyParser.sizeLimit`是最大允许解析的 Size，支持[bytes](https://github.com/visionmedia/bytes.js)，比如：

```
export const config = {
  api: {
    bodyParser: {
      sizeLimit: '500kb',
    },
  },
}
```

#### 支持 Connect/Express 中间件

你也可以使用[Connect](https://github.com/senchalabs/connect)兼容中间件。
比如，利用[cors 包]为 API[配置 CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。
首先，安装`cors`:

```
npm i cors
# or
yarn add cors
```

现在，让我们添加`cors`到 API 路由

```
import Cors from 'cors'

// Initializing the cors middleware
const cors = Cors({
  methods: ['GET', 'HEAD'],
})

// Helper method to wait for a middleware to execute before continuing
// And to throw an error when an error happens in a middleware
function runMiddleware(req, res, fn) {
  return new Promise((resolve, reject) => {
    fn(req, res, result => {
      if (result instanceof Error) {
        return reject(result)
      }

      return resolve(result)
    })
  })
}

async function handler(req, res) {
  // Run the middleware
  await runMiddleware(req, res, cors)

  // Rest of the API logic
  res.json({ message: 'Hello Everyone!' })
}

export default handler
```

#### 响应帮助

响应`res`包含一些类似于 express.js 的函数，提高开发者的体验并加快创建一个 API 路由的效率。看下下面的案例。

```
export default (req, res) => {
  res.status(200).json({ name: 'Next.js' })
}
```

包含下面的帮助：

- `res.status(code)` - 设置状态码，`code`一定是一个有效的 HTTP 状态码
- `res.json(json)` - 发送 json 响应。`json`一定是一个有效的 JSON 对象
- `res.send(body)` - 发送 HTTP 响应。`body`可以是`string`、`object`或者是`Buffer`

## 部署

## 高级特性

### 预览模式

### 动态导入

### 自动静态优化

如果没有要求阻塞的数据，Next.js 自动决定页面是否是静态的（可以被预渲染）。这个的判断是依据页面中是否存在`getInitialProps`。
这个特性允许 Next.js 打包出包含服务端渲染和静态生成页面的混合应用

> 静态生成的页面仍然是具有响应式的：Next.js 将混合你客户端的应用使其具有完整的交互性。

这个特性最大的好处是页面无服务端计算，可以立刻从多个 CDN 的位置流式传递给终端用户。给你的用户带来极致的加载体验。

#### 怎么做

如果`getInitialProps`存在，Next.js 将使用它默认的行为并且在后台渲染，提前请求（在服务端渲染的时候）
如果`getInitialProps`没有，Next.js 将自动静态化（预渲染页面成静态 HTML）。在预先渲染的时候，因为在这个阶段我们没有`query`信息，所以`query`对象是空的。混合之后，任何`query`值将填充到客户端。
`next build`将为静态优化页面构建出.html 文件。举例，`pages/about.js`页面将会生成这的结果

```
.next/server/static/${BUILD_ID}/about.html
```

如果你添加`getInitialProps`到页面，它的产物结果将是

```
.next/server/static/${BUILD_ID}/about.js
```

在开发环境下，如果`pages/about.js`被优化，会包含静态优化指示符。

#### 注意事项

- 如果你在`getInitialProps`中有自定义`App`,这个优化将对所有页面关闭
- 如果你在`getInitialProps`中有自定义`Document`，确保在服务端渲染之前就已经检查`ctx.req`已经被定义了。`ctx.req`在预渲染的时候是未定义的。

### 静态页面导出

### AMP 支持

### 自定义 Babel 配置

### 自定义 PostCSS 配置

### 自定义 Server

通常，你使用哪个 next start 来启动 next 服务。为了使用自定义路由模式，也可以百分百编程的方式启动服务。

> 在决定使用自定义服务之前请思考下只有当 Next.js 的路由无法满足你 APP 要求时才能使用。自定义服务将会删除重要的性能优化，比如无服务器功能以及[自动静态优化](https://nextjs.org/docs/advanced-features/automatic-static-optimization)

看下下面的自定义服务案例：

```js
// server.js
const { createServer } = require("http");
const { parse } = require("url");
const next = require("next");

const dev = process.env.NODE_ENV !== "production";
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  createServer((req, res) => {
    // Be sure to pass `true` as the second argument to `url.parse`.
    // This tells it to parse the query portion of the URL.
    const parsedUrl = parse(req.url, true);
    const { pathname, query } = parsedUrl;

    if (pathname === "/a") {
      app.render(req, res, "/b", query);
    } else if (pathname === "/b") {
      app.render(req, res, "/a", query);
    } else {
      handle(req, res, parsedUrl);
    }
  }).listen(3000, (err) => {
    if (err) throw err;
    console.log("> Ready on http://localhost:3000");
  });
});
```

> server.js 不会通过 babel 或者 webpack。确保文件的语法和源与你当前运行的 node 版本兼容。

然后，为了运行自定义 server，你需要更新 scripts 和 package.json。比如

```json
"scripts": {
  "dev": "node server.js",
  "build": "next build",
  "start": "NODE_ENV=production node server.js"
}
```

自定义服务使用以下导入将 Next.js 应用和 server 连接起来。
上面的 next 导入函数接收的是下面的参数对象：

- dev: Boolean - 是否在开发模式下启动 Next.js。 默认是 false
- dir: String - Next.js 项目的路径，默认是.
- quiet: Boolean - 隐藏包含服务信息的错误消息。默认是 false
- conf: object - 这个对象和在 Next.config.js 使用的一样的对象，默认是{}

返回的 app 可以让 Next.js 根据需求处理请求了

#### 关闭文件路由

默认，Next 用路径名匹配文件名来为在 pages 文件夹下的每个文件提供服务。如果你项目使用了自定义 server，这个行为可能会导致多个路径都可以为相同的内容提供服务，可能为 SEO 和用户体验造成问题。

```js
module.exports = {
  useFileSystemPublicRoutes: false,
};
```

> 注意 useFileSystemPublicRoutes 是简单的让服务端渲染禁用了文件路由；客户端路由可能仍可以访问路由。当使用这个选项时，你应该避免使用手动编程的方式连接到页面

> 你可能还需要配置客户端路由，防止客户端跳转到这个文件路由。参考[Router.beforePopState](https://nextjs.org/docs/api-reference/next/router#routerbeforepopstate)

### 自定义`App`

### 自定义 `Document`

### 自定义错误页面

### `src`目录

### 多个时间区

## 升级指南

## 常见问题

# API 文档

## CLI

## next/router

## next/head

我们暴露内置组件用于将元素添加到页面的顶部

```
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>My page title</title>
        <meta name="viewport" content="initial-scale=1.0, width=device-width" />
      </Head>
      <p>Hello world!</p>
    </div>
  )
}

export default IndexPage
```

为了避免在你的`head`中使用重复标签，你可以用`key`属性，这样会保证一个标签只会渲染一次。例子如下

```
import Head from 'next/head'

function IndexPage() {
  return (
    <div>
      <Head>
        <title>My page title</title>
        <meta
          name="viewport"
          content="initial-scale=1.0, width=device-width"
          key="viewport"
        />
      </Head>
      <Head>
        <meta
          name="viewport"
          content="initial-scale=1.2, width=device-width"
          key="viewport"
        />
      </Head>
      <p>Hello world!</p>
    </div>
  )
}

export default IndexPage
```

在这个例子中只有第二个会被渲染

> 卸载组件时会清除在`head`上的内容，保证每个页面需要的`head`都是干净完整定义，不用关心会有其他页面的 Head 干扰它

> `title`和`meta`元素是`Head`的直接子组件，或者包裹在`<React.Fragment>`下，否则 meta 标签不会正确的被服务端渲染找到

## next/amp

## Data Fetching

> 建议: 你正在阅读新文档。[旧文档](https://nextjs.org/docs/old)仍然有效

### getInitialProps

推荐：`getStaticProps`或者`getServerSideProps`
如果你使用 Next.js 9.3 或者更新，我们建议你使用它们来代替`getInitialProps`
这两个新获取数据的方法允许在静态生成和服务端渲染时选择粒度更细。更多信息看[Pages](https://nextjs.org/docs/basic-features/pages)和[Data fetching](https://nextjs.org/docs/basic-features/data-fetching)

**不贴 Examples**
`getInitialProps`启用页面中的服务端渲染，并允许你初始化数据填充。这意味着从服务端过来的页面携带了已经填充好的数据了。它对[SEO](https://en.wikipedia.org/wiki/Search_engine_optimization)非常友好。

> `getInitialProps` 将禁用自动静态优化

它是一个添加到任何页面的静态异步函数。看下面这些示例

```
import fetch from 'isomorphic-unfetch'

function Page({ stars }) {
  return <div>Next stars: {stars}</div>
}

Page.getInitialProps = async ctx => {
  const res = await fetch('https://api.github.com/repos/zeit/next.js')
  const json = await res.json()
  return { stars: json.stargazers_count }
}

export default Page
```

或者使用 class 组件

```
import React from 'react'
import fetch from 'isomorphic-unfetch'

class Page extends React.Component {
  static async getInitialProps(ctx) {
    const res = await fetch('https://api.github.com/repos/zeit/next.js')
    const json = await res.json()
    return { stars: json.stargazers_count }
  }

  render() {
    return <div>Next stars: {this.props.stars}</div>
  }
}

export default Page
```

`getInitialProps`用来异步获取数据，然后填充到`props`上
当服务端渲染时`getInitialProps`返回的数据是被序列化的。类似于`JSON.stringify`。保证返回的对象是一个 Plain 对象，而不是`Date`, `Map` or `Set`
对于初始化页面的加载，`getInitialProps`将只在服务端执行。只有当页面间跳转`next/link`或者使用`next/router`会在客户端执行。

### Context Object

### Caveats

### TypeScript

### Related

## next.config.js

### 介绍

想要自定义 next.js 的高级行为，你可以在项目的根目录下创建 next.config.js（在 package.json 旁边）。

next.config.js 是 Node.js 的常规模块，而不是 json 文件。它被 Next.js 的服务和它的构建阶段使用，并不会再浏览器构建时使用。

在下面的实例中：

```js
module.exports = {
  /* 在这里配置选项 */
};
```

你也可以使用函数：

```js
module.exports = (phase, { defaultConfig }) => {
  return {
    /* 在这里配置选项 */
  };
};
```

phase 是已经加载配置的当前上下文。你可以在[这里](https://github.com/zeit/next.js/blob/canary/packages/next/next-server/lib/constants.ts#L1-L4)看到有哪些 Phases。Phases 可以从 next/constants 导入：

```js
const { PHASE_DEVELOPMENT_SERVER } = require("next/constants");

module.exports = (phase, { defaultConfig }) => {
  if (phase === PHASE_DEVELOPMENT_SERVER) {
    return {
      /* 只在开发时用的配置 */
    };
  }

  return {
    /* 除了开发环境下的其他所有阶段的配置*/
  };
};
```

带注释的行是让你可以放配置的地方。

但是所有的配置都不强制要求，不需要知道每个配置都是干什么的。相反，你搜索你需要的属性去启用和修改，它将会显示你想要的结果。

### 环境变量

为了添加环境变量给 JS bundle，打开 next.config.js 文件并添加 env 配置：

```js
module.exports = {
  env: {
    customKey: "my-value",
  },
};
```

现在你可以在你的代码中访问 process.env.customKey。例如：

```js
function Page() {
  return <h1>The value of customKey is: {process.env.customKey}</h1>;
}

export default Page;
```

Next.js 将在构建时使用 my-value 替换 process.env.customKey。尝试对 process.env 进行解构不会工作，因为 webpack [DefinePlugin](https://webpack.js.org/plugins/define-plugin/) 的性质。

例如，下面一行

```jsx
return <h1>The value of customKey is: {process.env.customKey}</h1>;
```

将会被替换成：

```jsx
return <h1>The value of customKey is: {"my-value"}</h1>;
```

### 自定义页面扩展

和[@next/mdx](https://github.com/zeit/next.js/tree/canary/packages/next-mdx)的作用一样，mdx 支持页面以文件.mdx 结尾。你可以配置 pages 目录下那些扩展可以认为是页面。

打开 next.config.js 并添加 pageExtensions 配置：

```js
module.exports = {
  pageExtensions: ["mdx", "jsx", "js", "ts", "tsx"],
};
```

### 用资源前缀来支持 CDN

为了设置 CDN，你可以设置一个资源前缀并配置你 CDN 源去解析为 Next.js 项目运行所在的域名。

打开 next.config.js 并添加 assetPrefix 配置：

```js
const isProd = process.env.NODE_ENV === "production";

module.exports = {
  // Use the CDN in production and localhost for development.
  assetPrefix: isProd ? "https://cdn.mydomain.com" : "",
};
```

Next.js 将在加载的脚本自动使用你的前缀，但是对 public 目录下文件夹下的资源没有作用；如果你想要这写资源也用 CDN，你需要自己处理这个前缀。一种可以在你组件中因环境变量引入的前缀可以参考这个[案例](https://github.com/zeit/next.js/tree/canary/examples/with-universal-configuration-build-time)

### 构建目标

Next.js 支持各种构建目标，每个都会让你的应用改变构建和运行方式。我们将在下面解释每个目标。

#### server 目标

> 这是默认目标，然而，我们强烈建议使用无服务目标。无服务目标强制附加约束保证你走上正确的道路上。

这个目标和 next start 以及自定义服务设置兼容（自定义服务强制支持）

你的应用将被构建并部署成一个整体。这是默认目标，你不需要任何动作就可以加入。

#### serverless 目标

> 部署到[ZEIT Now](https://zeit.co/)将自动启用这个目标。你不需要自己加入。

这个目标输出独立的页面，不需要作为一个整体服务。

它只兼容 next start 或者无服务部署平台（like ZEIT Now）--你无法使用自定义的服务 API。

为了加入这个目标。在你的 next.config.js 中用以下配置：

```js
module.exports = {
  target: "serverless",
};
```

### 自定义 webpack 配置

### 压缩

### 静态优化指示器

当页面符合自动静态化优化条件时，我们会显示一个指示器让你知道。

它非常有用，如果页面符合条件，它会在开发环境下立刻执行自动静态优化并让你知道。

在某些情况下，指示器没用，比如在 electron 应用上工作时。为了在 next.config.js 删除并在 devIndicators 下禁用 autoPrerender 配置

```js
module.exports = {
  devIndicators: {
    autoPrerender: false,
  },
};
```

### 运行时配置

> 通常你想要为你的配置使用构建时环境变量。原因是因为运行时配置会增加渲染和初始化的开销，并且与自动静态化不兼容。
> 当使用 [serverless 目标](https://nextjs.org/docs/api-reference/next.config.js/build-target#serverless-target) 时运行时配置无效

为了添加运行时配置到你的应用，打开 next.config.js 并添加 publicRuntimeConfig 和 serverRuntimeConfig 配置。

```js
module.exports = {
  serverRuntimeConfig: {
    // Will only be available on the server side
    mySecret: "secret",
    secondSecret: process.env.SECOND_SECRET, // Pass through env variables
  },
  publicRuntimeConfig: {
    // Will be available on both server and client
    staticFolder: "/static",
  },
};
```

将只在服务端使用的运行时配置放到 serverRuntimeConfig 下

需要在客户端和服务端都要访问的变量应该放在 publicRuntimeConfig

> 依赖 publicRuntimeConfig 的页面必须使用 getInitialProps 来退出自动静态化。没有 getInitialProps，运行时配置将不再任何页面或者页面中的组件上有效。

使用 next/config 来访问运行时配置，比如

```jsx
import getConfig from "next/config";

// Only holds serverRuntimeConfig and publicRuntimeConfig
const { serverRuntimeConfig, publicRuntimeConfig } = getConfig();
// Will only be available on the server-side
console.log(serverRuntimeConfig.mySecret);
// Will be available on both server-side and client-side
console.log(publicRuntimeConfig.staticFolder);

function MyImage() {
  return (
    <div>
      <img src={`${publicRuntimeConfig.staticFolder}/logo.png`} alt="logo" />
    </div>
  );
}

export default MyImage;
```

### 禁止 x-powered-by

### 禁止 ETag 生成

### 设置自定义构建目录

你可以对自定义构建目录制定名字来替换.next

打开 next.config.js 并添加 distDir 配置：

```js
module.exports = {
  distDir: "build",
};
```

现在如果你运行 next build 将使用 build 来替换默认的.next 目录。

> distDir 不应该是你项目之外的目录。比如 ../build 是一个无效目录。

### 配置构建 ID

Next.js 使用构建时的生成的约定的 id 来识别你部署的应用是哪个版本。当在多服务上会造成麻烦（每个服务上都会执行 next build）。为了保证在各个构建之间保持同一个静态 id，你可以自己定义 id。

打开 next.config.js 并添加 generateBuildId 函数

```js
module.exports = {
  generateBuildId: async () => {
    // You can, for example, get the latest git commit hash here
    return "my-build-id";
  },
};
```

### 配置 onDemandEntries

### 忽略 TypeScript 错误

### exportPathMap

### 错误

- 在 getInitialProps 返回空对象
  **为什么出现这个错误**
  在页面组件中的`getInitialProps`返回空对象。它会取消自动静态化优化。如果你知道自己的操作并且知道后果。你可以在开发环境下忽略这个消息。
  **修复的方法** (我反正没看懂)
  查看`getInitialProps`返回空对象的页面。如果它们存在传递的组件，你可能需要给更新高阶组件添加`getInitialProps`
  **有用链接**
- [自动静态优化文档](Automatic Static Optimization Documentation)
