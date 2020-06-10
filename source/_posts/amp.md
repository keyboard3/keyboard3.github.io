---
title: 了解 amp
top: false
cover: false
toc: true
mathjax: true
date: 2020-06-06 19:51:23
password:
summary:
tags: [amp, 教程, 翻译, 进行中]
categories:
---

[原文 Welcome to the AMP community!](https://amp.dev/zh_cn/documentation/courses/beginning-course/?format=websites&level=beginner)

## 初级课程

### 欢迎来到 AMP 社区

#### 为什么有 AMP？

在许多方面，互联网是世界中心的枢纽。每天，来自全世界的许多人在线访问信息。但是许多人不是高速链接或者高性能设备上访问互联网，许多用户的体验不佳。

为了给网站访问者更好的体验，开发社区找到了提高网站性能的方法。在这个过程中开发者改善了辅助技术的用户的可访问性，变化无常的可靠性和各种设备上的网站设计。

有时候追踪学习所有 web 新技术非常困难。我们相信开发者想要更快速的网站，但是一路走来很容易出错。

AMP 就解决这些问题。AMP 目的是让开发者轻松的关注构建更好的功能而不用担心给用户带来不良的用户体验

#### AMP 有什么好处？

AMP 是一个 web 组件库用于实施最佳的 web 实践。AMP 解决了常见的开发障碍，以允许设计高性能，可访问和响应迅速的网站。

简而言之，AMP 让正确的事变得容易。让开发者腾出时间专注于给用户带来更有价值的功能。

AMP 致力于通过以下方式提高网站性能：

- 通过添加常见网站功能标签来扩展 HTML。HTML 是为了创建基本的内容页面开发的，但是还没发展到跟上现代网站的步伐。一些相关的现代网站功能包括侧滑导航菜单，视频播放器和图片轮播。为这些功能添加的额外标签被叫做“web 组件”

- 减少 JavaScript 数量。AMP 需要运行 JavaScript，但是 AMP 限制了页面上的 JavaScript 何时以及如何使用。这种限制可以极大的提高移动端网站性能。AMP 组件提供了许多开发者第一次使用 JavaScript 的功能。

- 在网站的开发过程中更早的发现问题。正如我们已经说过的，在现代 web 开发中有许多需要跟踪的地方。AMP 通过提供验证程序来帮我们来管理它们，验证程序用来帮我们找到网站中可能影响性能或访问性的问题。它还可以帮助你学习如何解决发现的问题。

AMP 的好处并不止于页面部署。像谷歌和微软都创建了缓存来存储没有验证错误的 AMP 页面。这些缓存对网站内容进行强大的性能优化，而不会影响到用户体验。缓存过的 AMP 页面还与搜索引擎集成在一起，所以你可以在几秒内甚至更少的时间从搜索结果到你的网站。

#### 学习 AMP 开发

学习 AMP 是学习 web 开发的好方法，因为 AMP 站点有：

- 使用标准的 HTML，CSS 和 JavaScript 构建
- 兼容所有现代浏览器
- 不依赖特殊工具或软件来在线部署

你将在构建 AMP 页面时获得技能，这些页面可以被转移到用其他的格式或者框架构建网站中。跟许多流行的框架一样，AMP 是基于组件的方式来设计和构建网站的。你讲学习到公认的构建最佳体验的网站，开始思考组件，并避免损坏用户的不良体验。这些通用的技能可以应用于整个 web。

AMP 解决了性能，可访问性和响应式设计问题，让你可以更加关注功能。但是，如果你磨炼你的技能，学习 AMP 也可以帮助你准确的了解 AMP 如何解决这些问题。你也将作为一名开发者继续学习和走向成熟，甚至你的用户享受 AMP 帮助你构建的体验。

## 课程介绍

### 这个课程适合谁

这个课程是为了有志向的新开发和希望提高网站性能的的开发设计的。在本课程和以下课程你将：

- 向你介绍 AMP 页面和传统“vanilla”网站的区别
- 使用实际的 AMP 页面和最佳体验逐步构建一个示例项目
- 学习构建现代网站的策略

### 先决条件

为了从这些课程中获得收益，你应该对 HTML 和 CSS 有基础的理解。足以识别 HTML 和 CSS 代码，并能够按照练习中的指示进行少量的添加和修改。请注意讲授这些概念超出了本课程的范围。如果需要，你可以在[HTML](https://developer.mozilla.org/en-US/docs/Web/HTML)和[CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)复习这些知识。

### 使用 Glitch 来跟踪代码

为了完成这些课程中的代码示例，我们将使用[Glitch](https://glitch.com/)。Glitch 是一个线上的代码编辑器，它允许你创建和预览网站而不需要在你电脑上安装任何东西。它也允许你创建一个服务，全部都在 Glitch 页面。

这个 Glitch 代码编辑环境看起来像这样：
![The online editor of Glitch](https://amp.dev/static/img/courses/beginner/image13.png?width=1000&hash=81846846441c0719ea8d81a85b15c447cbe5131d)

上面红色的框表明了你可以输入 HTML 和 CSS。绿色的框表示该按钮将带你进入创建页面的实时版本。黄色框的按钮让你可以复制这个项目并修改它。蓝色的框表示你可以使用的文件。在资源文件夹下，你可以找到你的图片。

在这些课程中，你将需要各种图片来完成练习。所有完成这些课程的图片都会被保存在我们的 Glitch 项目中。要查看项目中的图片，请在 Glitch 编辑器的左侧文件列表中点击资产条目。要获得任何单个图片的链接，请从右侧的资产资产列表中选中该图片。在弹出的弹框中，点击 URL 旁边的赋值按钮。然后你可以在任何需要的地方使用这个链接。
![The assets view in Glitch](https://amp.dev/static/img/courses/beginner/image8.png?width=1000&hash=70e74330b4a3cf41bd91d88ecc2e376d991bf509)
![The details pop-up (including URL) for an image in the assets collection](https://amp.dev/static/img/courses/beginner/image12.png?width=1200&hash=7dd67d72f09d12373617986571a37d7b47beeb91)

在这个课程中，我们将从最基本的 HTML 页面开始。我们将在 Glitch 上创建一个空的项目，其包含一些图片，你稍后需要的服务器代码，带标题和一张图片的 index.html 文件。

打开[这个](https://glitch.com/edit/#!/nosy-leech)项目开始。点击右上角的“Remix This”按钮创建一个可以编辑的新项目。你可以在本课程和以后的课程继续使用这个编辑器。以后的每门课程都会给你提供该点开始的解决方案参考版本的机会。

你无需使用 Glitch 来完成这些培训。然而，一些完成练习所需要的代码值包含在 Glitch 示例上。如果你喜欢使用其他编辑器，你仍需要使用 Glitch 示例来找到这些代码。

### 设置 AMP 验证器

为了检测我们 AMP 页面的错误，我们有一个有价值的工具：AMP 验证器。编写有效的 AMP 页面是获得框架全部收益的关键。AMP 验证器可以通过两种方式访问：通过 Chrome 扩展程序，或者是通过在我们的 URL 上添加参数，所以我们需要在代码中内置验证器。就本课程来说，我们建议你使用 Chrome 扩展程序，因为它在构建你网站时更容易使用和访问。

- 安装 Chrome 扩展程序，访问[链接](https://chrome.google.com/webstore/detail/amp-validator/nmoffdblmcmgeicmolmhobpoocbbmknc/related?hl=en)
- 要改为内置的 AMP 验证器，在你 AMP 页面 URL 地址末尾添加#development=1，然后在你浏览器控制台查看结果。如果你使用 Chrome 扩展程序你不需要添加这个参数。

### 我们将构建什么

在我们三个课程中，你将为 Chico 的奶酪自行车商城建立一个网站。Chico 开发一款完全由奶酪制成的革命性自行车。对自行车需求如此之高让 Chico 需要尽可能快的构建一个网站。当我们完成这三门课程，Chico 的网站将看起来这样：
![How the site looks at the end of the Advanced Course](https://amp.dev/static/img/courses/beginner/image14.png?width=330&hash=1b27ffdd511e8550ae8920116b236cdf74c50bd0)
你可以点击[这个](https://nice-consonant.glitch.me/)链接来看到实时的预览。纵观整个网站。我们有视频，注册表单，图片轮播，分享到其他媒体的方式。通过点击左上角的三行图标（也叫做汉堡菜单图标）。一旦菜单展开，点击我们的产品链接导航到产品列表。尝试按照价格对产品列表排序，并按照产品分类过滤产品列表。

我们选择 Chico 的网站作为我们的模型，因为它提供了我们今天在流行网站上常见的功能集合。它完全使用 AMP 来构建。在这些课程中，我们将从头开始构建此站点。

## 我们的第一个 AMP 页面

### 开始我们的旅程

这是我们团队第一天构建 Chico 的奶酪自行车网站。到目前为止，这个网站是基础的 HTML 页面，表头包含网站的标题，一个我们自行车的图片，一些营销文字。
![Our basic HTML website](https://amp.dev/static/img/courses/beginner/image17.png?width=1000&hash=fe919f49d78010e4d32814e1067b0190b46fc608)
在你的 Glitch 项目中，打开 index.html 并查看 HTML：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Chico's Cheese Bicycles</title>
    <link
      rel="shortcut icon"
      href="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fcheese-favicon.png?1540228964214"
    />
  </head>
  <body>
    <header class="headerbar"><h2>Chico's Cheese Bicycles</h2></header>
    <main>
      <div class="main-content">
        <img
          src="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fricotta-racer.jpg?1540228217746"
        />
        <div class="below-hero">
          <h2 class="main-heading">What we're about</h2>
          <p class="main-text">
            We sell the only ten-speed bicycles in the world made entirely of
            cheese. They get you where you need to go, and you never arrive
            hungry. Our bikes are 100% biodegradable. And with our new
            rodent-repelling varnish they last longer than ever!
          </p>
        </div>
      </div>
    </main>
  </body>
</html>
```

我们团队已经确定使用 AMP 将构建所需要的网站功能更加容易，因此我们的工作是将这个 HTML 页面转换成有效的 AMP 页面。

首先我们需要将全世界表明我们正在尝试构建 AMP 网站。为此，我们将为 html 标签加上一些装饰。如果 html 标签包含 ⚡ 符号或者单词`amp`，AMP 验证程序和 AMP 缓存会将我们的网站认为是 AMP 站点。

在你的 Glitch 项目，添加 ⚡ 符号到 html 标签，像这样：

```html
<html ⚡ lang="en"></html>
```

这个符号是必须的。表明我们正在构建 AMP 页面。下一步，我们讨论像 AMP 验证器这样的工具如何帮助我们确定使网站有效需要哪些更改。

### 验证和 AMP 缓存

我们已经多次提到一个概念：编写有效的 AMP。让我们讨论一下它是什么意思，以及为什么它重要。

首先，有效的 AMP 是很重要，因为它对你的用户有好处。AMP 的规则代表了性能，可访问性和安全的最佳实践。但是验证错误是 AMP 告诉你仍有空间为你的用户改善网站。

其次，AMP 重要的原因是有用的 AMP 缓存。这个缓存是 AMP 体系结构中重要的组成部分。它是内容分发网络(CDN)，目的是：

- 只服务有效的 AMP 页面
- 允许 AMP 页面去高效和安全的预加载
- 在缓存中的页面上做一些性能优化

当用户请求你的 AMP 网站，它们可能是从就近的 AMP 缓存服务器发送给它。如果你的网站在 AMP 缓存中，那么当你使用 Google 之类的搜索引擎时，可以高效的预加载在到后台。如果用户在搜索结果中选中你的网站，即使连接不良，它也会在几秒内显示出来，这个 AMP 缓存将你站点上执行自动优化，例如：

- 缓存字体
- 缓存和压缩图片，转换它们成 WebP 这样的新格式
- 对 AMP 文档消毒以阻止跨站脚本攻击或者其他漏洞
- 修复可能导致在各种浏览器下渲染不一致的 HTML 问题;例如，关闭所有标签，小写属性名或者转义文本

当你使用 AMP 构建网站并完成这些训练中的练习时，检查保证你的网站有效。为了跟踪这些验证错误，我将使用在[课程介绍](https://amp.dev/zh_cn/documentation/courses/beginning-course/course-introduction/?format=websites&level=beginner#setting-up-the-amp-validator)中已经安装的 AMP 验证器。

### 练习 1：使用 AMP 验证器

安装 AMP 验证器 chrome 扩展程序之后，这个验证器将自动在你打开的任何带有 html 标签中包含 AMP 符号(⚡)的页面上运行，像我们现在这样。打开你的 Glitch 项目并查看 AMP 验证器扩展程序的图标。它应该看起来类似于红色上有一个徽章，表明了有 7 个验证错误。
![The AMP Validator Chrome extension showing AMP issues.](https://amp.dev/static/img/courses/beginner/image6.png)
点击 AMP 验证器的图标打开弹窗，显示当前页面的验证错误列表，并给出这些错误可能的解决方案。
![The issues displayed in the AMP Validator Chrome Extension.](https://amp.dev/static/img/courses/beginner/image22.png?width=2500&hash=7ee74f950b8b863213940e0dc221a38a4c7c850c)
img 标签这一项：

```
The tag <img> may only appear as a descendant of tag 'noscript'. Did you mean 'amp-img'?
```

点击这一项末尾的 Debug 链接。这个 Debug 链接将带你直接跳转到你页面上包含这个错误的代码行。它帮助你在你文件中找到遇到的错误，并提供所需的上下文来理解如何解决这个错误。不用担心，这个消息现在似乎尚不清楚，但这很容易解决。我们需要使用 AMP 组件 <amp-img> 来替换 HTML 标签 <img> 。在本课程的[组件的思考](https://amp.dev/zh_cn/documentation/courses/beginning-course/thinking-in-components/?format=websites&level=beginner)这一节中，我们将探索为什么会出现这个错误，<amp-img> 是什么，然后如何解决它。
![AMP debugger showing an error inline.](https://amp.dev/static/img/courses/beginner/image16.png?width=2500&hash=73a6f348dbdad8011aa80519378e74a8c4442b14)
对于其他的验证错误项，点击"Learn more"的链接标签。这个链接将你从错误描述中转到响应的 AMP 文档，帮助你修复这个问题。
![AMP documentation reached via the “Learn more” link in the AMP Validator.](https://amp.dev/static/img/courses/beginner/image21.png?width=1750&hash=06f514eceeae89e6bf72c99bfc23ea619f71f6af)
下一步是修复这些验证问题。为此，我们需要更加的了解 AMP 页面必要的元素。除了向我们的 HTML 中添加闪电符号之外我们还需要做更多的事情创建一个有效的 AMP 页面。

### AMP 页面的剖析

每个页面都以相同的基础模板开头。然后我们自定义并从那里构建页面。这个入门模板有时候叫做 AMP 样板。这个样板是设置 AMP 运行时并帮助 AMP 页面运行的更加平滑的标签组合。

在这一节，我们将对 AMP 样板的每个部分都进行一些解释。然而，你无需记住在你用 AMP 创建的每个页面添加这些标签。你可以简单的用这个样板开始构建 AMP 页面。

下面的这些标签是 AMP 页面必须的。有效的 AMP 页面一定包含：

- 从 `<!doctype html>` doctype 开始
- 包含 `<head>` 和 `<body>` 标签
- `<head>` 内的第一个标签是 `<meta charset="utf-8">` 标签
- 在 `<head>` 标签中包含 `<meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">`。注意：建议 initial-scale=1

下面这些规则专门用于设置 AMP 运行时。验证 AMP 页面一定包含：

- 包含一个顶层的`<html ⚡>`标签。这个闪电象征符号表明它是一个 AMP 站点。注意：`<html amp>`也接受。
- 在它们的`<head>`标签中包含一个`<script async src="https://cdn.ampproject.org/v0.js"></script>`标签。加载 AMP JavaScript 库。注意：作为最佳实践，你应该在`<head>`中尽量早的包含这个脚本。
- 在`<head>`中包含一个`<link rel="canonical" href="$SOME_URL">`标签。如果存在指向常规的 HTML 版本，或者如果不存在非 AMP 的版本就指向自身。注意：你应该在你页面上替换`$SOME_URL`为真实的地址。
- 在`<head>`标签中包含 AMP 样式的样板代码。该 CSS 在页面上隐藏直到 AMP 库加载完成。以下是 AMP 样式的样板代码：

```html
<style amp-boilerplate>
  body {
    -webkit-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    -moz-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    -ms-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    animation: -amp-start 8s steps(1, end) 0s 1 normal both;
  }
  @-webkit-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @-moz-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @-ms-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @-o-keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }
  @keyframes -amp-start {
    from {
      visibility: hidden;
    }
    to {
      visibility: visible;
    }
  }</style
><noscript
  ><style amp-boilerplate>
    body {
      -webkit-animation: none;
      -moz-animation: none;
      -ms-animation: none;
      animation: none;
    }
  </style></noscript
>
```

### CSS 和 AMP

CSS 自定义你网站的外观。你将几乎总是添加自定义样式到你的 AMP 页面上。不过，注意 AMP 对 CSS 的使用做了一些限制：

- 样式只能存在于`<style amp-custom>`标签内的文档头，或在需要时作为内联样式使用。这个限制阻止加载外部样式表，但是也保存网络请求，启用缓存并提高性能。
- 一个 AMP 页面只能包含一个`<style amp-custom>`标签(装饰的样式标签)
- 一个页面最多包含 75k 的 css
- `!important`规则受限制
- 更多的关于 CSS 规则的限制，请查看[此处](https://amp.dev/zh_cn/documentation/guides-and-tutorials/develop/style_and_layout/style_pages/?format=websites)的文档

要练习将自定义的样式添加到 AMP，请在`<head>`的页面上添加`<style amp-custom>`标签，看看会发生什么。完成后，你可以从你的页面中删除它。

```html
<style amp-custom>
  body {
    font-family: sans-serif;
    line-height: 1.5rem;
    padding: 20px;
  }
  p,
  h2 {
    border: 1px dotted red;
  }
</style>
```

![Custom CSS affecting our page.](https://amp.dev/static/img/courses/beginner/image10.png?width=560&hash=e71123278226e4245149ac2b1d9f11ea83a1f484)

### 练习 2：转换我们 HTML 页面的剩余部分

现在是时候修正上一节练习在我们网站上发现的验证错误了。为此，我们需要给我们基础 HTML 网站添加缺失的 AMP 样板代码。

对此以及所有以后的练习，我们将应用所学的知识对我们的 Glitch 网站上的真实代码进行更改。我们会给你一些提示。在每次练习结束之后，我们将给出一个完整的解决方案。尝试依靠自己完成练习，但是如果你卡主了或者是需要提示，可以随时从解决方案一节中复制代码。

另外，在每一节课程的开头和末尾，我们提供一个包含我们到此为止完成的所有的代码。如果你丢失了当前的 Glitch 页面或者想从我们的解决方案开始，你可以从 Glitch 示例中复制代码，或者重新混合这些示例并从那里继续前行。

使用 AMP 的[文档](https://amp.dev/zh_cn/documentation/guides-and-tutorials/start/create/basic_markup/?format=websites)和上面的注释，更新你的 Glitch 项目，你会发现只有`<img>`标签验证错误依旧存在。另外，为了帮助我们构建 Chico 的奶酪自行车网站，我们已经提供了一些样式，可以在整个培训中使用。如果你打开[这个](https://pastebin.com/vNws2bA1)页面，`<style amp-custom>`标签包含你所需的样式。你应该复制这些样式到你正在工作的项目中去。

#### 解决方案

可以在[https://glitch.com/~hungry-modem]这个 Glitch 示例中找到解决方案。这个页面包含改动的部分应该如下所示：

```html
<head>
  <meta charset="utf-8" />
  <script async src="https://cdn.ampproject.org/v0.js"></script>
  <title>Chico's Cheese Bicycles</title>
  <link
    rel="shortcut icon"
    href="https://cdn.glitch.com/d7f46a57-0ca4-4cca-ab0f-69068dec6631%2Fcheese-favicon.png?1540228964214"
  />
  <meta
    name="viewport"
    content="width=device-width,minimum-scale=1,initial-scale=1"
  />
  <link rel="canonical" href="https://hungry-modem.glitch.me/" />
  <style amp-boilerplate>
    body {
      -webkit-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
      -moz-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
      -ms-animation: -amp-start 8s steps(1, end) 0s 1 normal both;
      animation: -amp-start 8s steps(1, end) 0s 1 normal both;
    }
    @-webkit-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @-moz-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @-ms-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @-o-keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
    @keyframes -amp-start {
      from {
        visibility: hidden;
      }
      to {
        visibility: visible;
      }
    }
  </style>
  <noscript
    ><style amp-boilerplate>
      body {
        -webkit-animation: none;
        -moz-animation: none;
        -ms-animation: none;
        animation: none;
      }
    </style></noscript
  >
  <style amp-custom>
    ... styles elided due to length ...;
  </style>
</head>
```

### 一个几乎有效的 AMP 页面

如果你完成了上述的所有练习，恭喜！你的页面几乎是一个有效的 AMP 页面。如果你打开你的页面并打开 Chrome DevTools(`Command+Option+I 在Mac上 或者 Control+Shift+I 在Windows/Linux`)并选中 Console 标签页，你应该在控制台中看到这个消息，表示这个 AMP 库被成功的加载了：

```
Powered by AMP ⚡ HTML
```

接下来，如果你打开 AMP 验证扩展程序，会显示我们犯下了最后一个错误：

```
The tag 'img' may only appear as a descendant of tag 'noscript'. Did you mean 'amp-img'?
```

这是需要理解的重要错误。一些 HTML 标签不允许出现在 AMP 文档中。在某些情况下，AMP 要求你使用其他来替换。我们叫一些自定义非 HTML 标签的标签为”组件“，并且我们将稍后在本训练的下一节来讨论它们。
