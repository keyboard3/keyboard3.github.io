---
title: animate.js 文档
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-12 21:44:27
password:
summary:
tags: [js, animate, 翻译]
categories:
---

[原文](https://animejs.com/documentation/)

## 目标

### CSS 选择器

可以作用到任何 CSS 选择器上。

> 伪类元素使用 js 无法作为其目标

| TYPE   | DEFAULT | EXAMPLE        |
| ------ | ------- | -------------- |
| String | null    | 目标是: 'item' |

**代码示例**

```js
anime({
  targets: ".css-selector-demo .el",
  translateX: 250
});
```

### DOM 单个节点/节点数组

可以是 DOM 节点或者是节点列表
| TYPE | DEFAULT | EXAMPLE |
| ------ | ------- | -------------- |
| DOM Node | null | 目标是: el.querySelector('.item') |
| NodeList | null | 目标是: el.querySelectorAll('.item') |

**代码示例**

```js
var elements = document.querySelectorAll(".dom-node-demo .el");

anime({
  targets: elements,
  translateX: 270
});
```

### js 对象

至少包含一个数值值属性的 js 对象

| TYPE   | DEFAULT | EXAMPLE              |
| ------ | ------- | -------------------- |
| Object | null    | 目标是: myObjectProp |

**代码示例**

```js
var logEl = document.querySelector(".battery-log");

var battery = {
  charged: "0%",
  cycles: 120
};

anime({
  targets: battery,
  charged: "100%",
  cycles: 130,
  round: 1,
  easing: "linear",
  update: function() {
    logEl.innerHTML = JSON.stringify(battery);
  }
});
```

### 数组

包含多个目标的数组

> 接受混合类型。如 `['.el', domNode, jsObject]`

| TYPE  | DEFAULT | EXAMPLE                                        |
| ----- | ------- | ---------------------------------------------- |
| Array | null    | 目标是: ['.item', el.getElementById('#thing')] |
|       |

**代码示例**

```js
var el = document.querySelector(".mixed-array-demo .el-01");

anime({
  targets: [el, ".mixed-array-demo .el-02", ".mixed-array-demo .el-03"],
  translateX: 250
});
```

## 属性

### CSS 属性

### CSS 转换

### 对象属性

### DOM 属性

### SVG 属性

## 属性的参数

### duration

### delay

### end delay

### easing

### round

### 特定属性的参数

### 基于功能的参数

## 动画参数

### 方向

### loop

### autoplay

## 值

### 单位

### 指定单位

### 相对计算

### 颜色

### 起始值

### 基于功能的值

## Keyframes

### 动画关键帧

### 属性帧

## STAGGERING

### STAGGERING 基础

### start 值

### range 值

### from 值

### direction

### easing

### grid

### axis

## 时间轴

### 时间轴基础

### 时间偏移

### 参数继承

## 控制

### 播放和停止

### 重启

### 反向

### seek

### 时间轴控制

## callbacks and promises

### update

### begin & complete

### loopBegin & loopComplete

### changeBegin & changeComplete

### finished promise

## SVG 动画

### 运动路径

### 变形

### 线图

## 缓动函数

### 线性

### penner's 函数

### cubic 贝塞尔曲线

### 弹簧

### 弹力

### 步数

### 自定义缓动函数

## Helpers

### remove

### get

### set

### random

### tick

### running
