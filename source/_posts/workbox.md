---
title: workbox
top: false
cover: false
toc: true
mathjax: true
date: 2020-06-16 09:46:47
password:
summary:
tags: [service-work, workbox, 翻译, 翻译中]
categories:
---

[workbox/guides 原文](https://developers.google.com/web/tools/workbox/guides/get-started)

# 开始使用

## 创建和注册一个 service worker 文件

## 导入 workbox

### 从 CDN 中

### 使用打包器

## 使用 workbox

## workbox 可以做什么？

# 路由匹配请求

## 使用字符串匹配路由

## 使用正则匹配路由

## 使用回调函数匹配路由

## 使用 workbox 策略处理路由

## 使用自定义回调来处理路由

# 配置 workbox

## 策略中自定义缓存名

## 策略中自定义 fetch 的配置项

## 配置 Debug 构建 vs 生产构建

## 禁止打印

# 处理第三方请求

## 跨域请求和不透明响应

### 记得选择加入跨域模式

## workbox 有时候缓存不透明的响应

## 强制缓存不透明的响应

# 使用插件

## workbox 插件

## 自定义插件

## 第三方插件

# 启用离线 Google 分析

# 使用打包器打包 workbox(webpack/Rollup)

## 导入 workbox 代码到你的包中

### 确定导入文件的路径

### 从 importScripts 移动到模块导入

## 将仅开发人员的代码保留到包中

### webpack

### rollup

## 使用捆绑器和 workbox 的构建工具一起使用

### workbox-build

### workbox-webpack-plugin

## 代码分割和动态导入

## ES2015 语法

## 第三方捆绑器插件

# 诊断和调试

## 了解您的开发人员工具

### 更新和重新加载

### 清除存储

## 绕过网络

## 常见问题

## 调试 workbox

### 使用 debug 构建

### 启用 debug 日志

## Stack Overflow

# 明白存储限制

## 支持哪些配置项？

### maxEntries

### maxAgeSeconds

### purgeOnQuotaError

## 你允许存储多少数据

### 指定 Chrome 无痕模式注意事项

## 当心不透明响应

# 常用的实践

## Google 字体

## 缓存图片

## 缓存 CSS 和 JavaScript 文件

## 从多个来源缓存内容

## 限制从指定源缓存

## 强制网络请求超时

## 从指定的子目录下缓存资源

## 基于资源类型缓存资源

## 从 web 应用的代码中访问缓存

# 高级实践

## 为用户提供页面重新加载

## "预热"运行时缓存

## 提供路由的备份响应

### 仅离线应用

### 全面的备份

## 使用策略发出独立请求

## 缓存音频和视频

# service worker 清单

## 正确的注册你的 service worker

## 进一步学习
