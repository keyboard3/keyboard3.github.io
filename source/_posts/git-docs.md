---
title: git docs
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-09 11:51:24
password:
summary:
tags: [git, 翻译, 进行中]
categories:
---

[原文 git-scm.com/docs](https://git-scm.com/docs)

## 配置

## 获取和创建项目

## 基础快照

## 分支和合并

## 分享和更新项目

### fetch

### pull

### push

#### 选项

- --delete
  从远程仓库中删除所有列出来的 refs。与所有 refs 前加上:号道理一样。

### remote

#### 名称

git-remote - 管理一组跟踪的仓库

#### 概要

```jsx
git remote [-v | --verbose]
git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
git remote rename <old> <new>
git remote remove <name>
git remote set-head <name> (-a | --auto | -d | --delete | <branch>)
git remote set-branches [--add] <name> <branch>…​
git remote get-url [--push] [--all] <name>
git remote set-url [--push] <name> <newurl> [<oldurl>]
git remote set-url --add [--push] <name> <newurl>
git remote set-url --delete [--push] <name> <url>
git remote [-v | --verbose] show [-n] <name>…​
git remote prune [-n | --dry-run] <name>…​
git remote [-v | --verbose] update [-p | --prune] [(<group> | <remote>)…​]
```

#### 描述

管理你跟踪的远程仓库分支

#### 选项

```
-v
--verbose
```

稍微冗长一点并在那么后面显示远程地址。注意，它必须放在 remot 和 subcommand 之间。

#### 命令

- add
- rename
- remove
- rm
- set-head
- set-branches
- get-url
- set-url
- show
- prune
- update

#### 讨论

#### 案例

- 添加一个新的远程分支，获取并从它检出

```
$ git remote
origin
$ git branch -r
 origin/HEAD -> origin/master
 origin/master
$ git remote add staging git://git.kernel.org/.../gregkh/staging.git
$ git remote
origin
staging
$ git fetch staging
...
From git://git.kernel.org/pub/scm/linux/kernel/git/gregkh/staging
* [new branch]      master     -> staging/master
* [new branch]      staging-linus -> staging/staging-linus
* [new branch]      staging-next -> staging/staging-next
$ git branch -r
 origin/HEAD -> origin/master
 origin/master
 staging/master
 staging/staging-linus
 staging/staging-next
$ git switch -c staging staging/master
...
```

- 模拟 git clone 但是跟踪选中的分支

```
$ mkdir project.git
$ cd project.git
$ git init
$ git remote add -f -t master -m master origin git://example.com/git.git/
$ git merge origin
```

#### 也可以看看

git-fetch[1] git-branch[1] git-config[1]

### submodule

## 检验和比较

## 补丁

## 调试

## 指南

## Email

## 外部系统

## 管理

## 服务管理

## 高级命令
