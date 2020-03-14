---
title: braft-editor
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-12 23:12:03
password:
summary:
tags: [editor,html]
categories:
---

# [braft-editor](https://github.com/margox/braft-editor)
# [官方文档](https://www.yuque.com/braft-editor/be/gz44tn#gug9gs)

## 场景

轻量级的一个支持富文本编辑的网页版编辑器。

## 原理推测

其实相当于将网页开发语言的编辑工具化，让普通人就可以通过简易的编辑器的点击操作就能够完成写代码才能完成的绚丽的富文本展示。
本质就是对特定样式效果处理给页面操作化。
因为功能本身是一个正交的，对文本的影响可以相互叠加，需要在编辑文本和效果文本之间做操作的映射，所以这块逻辑管理应该非常复杂。
推测组件除了控制各个粒度文本显示的样式之外，还必须能监控到内部富文本和纯文本只之间的数据交换的过程。