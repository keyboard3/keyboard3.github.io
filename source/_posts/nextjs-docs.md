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
### getInitialProps
> 建议: