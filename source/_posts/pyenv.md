---
title: pyenv
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-30 10:31:11
password:
summary:
tags: [python, 翻译, 进行中]
categories:
---

[Simple Python Version Management: pyenv](https://github.com/pyenv/pyenv)

## 介绍

pyenv 让你更容易的在多个 Python 版本中切换。它使用非常简单，跟传统 UNIX 只关注一件事的工具一样。
这个项目从[rbenv](https://github.com/rbenv/rbenv)和[ruby-build](https://github.com/rbenv/ruby-build)中 fork 出来，为了 python 修改
** pyenv 功能 **

- 让你基于每个用户更改全局 Python 版本
- 提供对么个项目的 python 版本
- 允许你用环境变量重写 Python 版本
- 一次可以搜索多个 Python 版本的命令。对于使用 tox 扩 python 版本测试非常用帮助

## 命令参考

[Command Reference](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md)
跟 git 一样,pyenv 根据第一个参数代理成子命令
公共的子命令如下

- pyenv commands
- pyenv local
- pyenv global
- pyenv shell
- pyenv install
- pyenv uninstall
- pyenv rehash
- pyenv version
- pyenv versions
- pyenv which
- pyenv whence
