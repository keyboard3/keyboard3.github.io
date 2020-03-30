---
title: venv
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 09:44:15
password:
summary:
tags: [python3, 翻译, 未完待续]
categories:
---

[venv — Creation of virtual environments](https://docs.python.org/3/library/venv.html)
Version 3.3

## 介绍

Venv 支持使用自己的站点目录来创建轻量级的“虚拟环境”，有选择的与系统站点目录隔离。每个虚拟环境有自己的 Python binary (匹配用于创建对应虚拟环境的 python 二进制文件)，可以在自己的站点目录下有自己独立安装的 Python 依赖包。
可以通过 [PEP 405](https://www.python.org/dev/peps/pep-0405) 来看更多关于 Python 的虚拟环境的信息

## 创建虚拟环境

通过执行命令`venv`来创建[虚拟环境](https://docs.python.org/3/library/venv.html#venv-def)
`python3 -m venv /path/to/new/virtual/environment`
运行这个命令创建目标目录（如果父目录不存在也会创建），并会将 pyvenv.cfg 文件放到其中，会将`home`key 指向到运行命令的 python 安装(目录的通用名字是.venv)。它会创建 bin 子目录(在 windows 上是 Scripts)，包含 Python 二进制文件的复制/符号链接 (适用环境创建时的平台或参数)。它同时也会创建空的`lib/pythonX.Y/site-packages`目录 (在 windows 上是`Lib\site-packages`)。如果目录已存在就会重用。

## 未完待续
