---
title: nextjs-docs
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 10:47:26
password:
summary:
tags: [nextjs,翻译,未完待续]
categories:
---
[nextjs-docs](https://nextjs.org/docs/getting-started)

# 文档
## 介绍
如果你是新人，建议你学习改[课程](https://nextjs.org/learn/basics/getting-started)
带有测试的交互课程会指导你完成使用Next.js所需要的知识
如果你有任何有关Next.js的问题，你可以去[社区](https://github.com/zeit/next.js/discussions)中提问
**系统要求**
 - Node.js 10以上
 - MacOS，windows(包括WSL),以及Linux

...

# API文档
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
> 卸载组件时会清除在`head`上的内容，保证每个页面需要的`head`都是干净完整定义，不用关心会有其他页面的Head干扰它

> `title`和`meta`元素是`Head`的直接子组件，或者包裹在`<React.Fragment>`下，否则meta标签不会正确的被服务端渲染找到

## Data Fetching
> 建议: 你正在阅读新文档。[旧文档](https://nextjs.org/docs/old)仍然有效

### getInitialProps
推荐：`getStaticProps`或者`getServerSideProps`
如果你使用Next.js 9.3或者更新，我们建议你使用它们来代替`getInitialProps`
这两个新获取数据的方法允许在静态生成和服务端渲染时选择粒度更细。更多信息看[Pages](https://nextjs.org/docs/basic-features/pages)和[Data fetching](https://nextjs.org/docs/basic-features/data-fetching)

**不贴Examples**
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
或者使用class组件
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
当服务端渲染时`getInitialProps`返回的数据是被序列化的。类似于`JSON.stringify`。保证返回的对象是一个Plain对象，而不是`Date`, `Map` or `Set`
对于初始化页面的加载，`getInitialProps`将只在服务端执行。只有当页面间跳转`next/link`或者使用`next/router`会在客户端执行。
### Context Object
### Caveats
### TypeScript
### Related