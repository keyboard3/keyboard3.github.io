---
title: nextjs-examples
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-11 10:41:20
password:
summary:
tags: [next.js, 翻译, 进行中]
categories:
---

这个文档时针对实例中出现可能有表达案例意图的内容而添加的

## 使用通用的构建时配置

[原始地址](https://github.com/zeit/next.js/tree/canary/examples/with-universal-configuration-build-time)
这个案例展示了如何使用环境变量，并使用 dotenv，一个.env 文件和 next.config.js 来基于应用的 NODE_ENV 定制这些环境变量。

当你构建应用，环境变量被转化成原语（字符串或者是未定义），只有在新的构建时它才会被改变。同样会作用到客户端和服务端上。如果环境变量被应用直接使用，变更变量它只会影响服务端，客户端则不受影响。

### 自己部署

使用[ZEIT Now](https://zeit.co/now)来部署这个案例

> ps:我这里没有，去上面的原文点击部署

### 如何使用这个案例（省略）

用[ZEIT Now](https://zeit.co/now)（[文档](https://nextjs.org/docs/deployment)）来部署到云端

### 注意

- 提交 env 变量到仓库是非常坏的影响。这是为什么你应该在[gitignore](https://git-scm.com/docs/gitignore)忽略.env 文件
- 任何你在 next.config.js 中暴露的变量都是公共可以被客户端访问的变量。
- 这个案例是在构建时设置环境变量，意味着基于运行环境预发布/生产的环境变量无法使用。针对运行时的环境变量解决方案，参考案例[使用运行时环境变量](https://github.com/zeit/next.js/blob/canary/examples/with-universal-configuration-runtime)
- 如果你在.env 中有许多变量并不想在 next.config.js 中都列出来，看[使用 dotenv](https://github.com/zeit/next.js/blob/canary/examples/with-dotenv)案例。这个案例自动暴露变量并在代码中引用，但是将所有其他变量保密。
