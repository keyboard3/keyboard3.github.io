---
title: webpack 配置之外部扩展
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-12 14:26:36
password:
summary:
tags: [webpack, configuration, externals, 翻译, 进行中]
categories:
---

[原文](https://webpack.js.org/configuration/externals/#externals)

这个外部扩展配置选项提供了不在输出的打包文件中包括依赖的方式。相反，这个创建的包文件依赖已存在用户端的环境（任何用户侧的应用）。这个特性对于库开发者非常有用，很多各种应用都在这么使用。

## externals

**string**,**[string]**,**object**,**function**,**RegExp**

阻止导入指定的包，用在运行时获取外部依赖来代替。
例如，为了从 CDN 引入 jQuery 而而不是打包它：
**index.html**

```html
<script
  src="https://code.jquery.com/jquery-3.1.0.js"
  integrity="sha256-slogkvB1K3VOkzAI8QITxV3VzpOnkeNVsKvtkYLMjfk="
  crossorigin="anonymous"
></script>
```

**webpack.config.js**

```js
module.exports = {
  //...
  externals: {
    jquery: "jQuery",
  },
};
```

所有依赖模块都没有发生改变，代码仍然可以工作：

```js
import $ from "jquery";

$(".my-element").animate(/* ... */);
```

使用外部依赖的带包可以使用各种模块的上下文，像[CommonJS,AMD,global and ES2015 modules](https://webpack.js.org/concepts/modules).这些外部依赖库可以用以下形式提供：

- **root**: 库作为全局变量（通过 script 标签引入）
- **commonjs**: 库作为 CommonJS 模块
- **commonjs2**: 跟上面类似，但是导出的是`module.exports.default`
- **amd**: 类似于`commonjs`但是使用 ADM 模块系统

接受以下语法

### string

### [string]

### object

### function

### RegExp

### Combining syntaxes
