---
title: k8s-tasks-configure-pods-and-containers
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-11 14:18:18
password:
summary:
tags: [k8s, 翻译, 进行中]
categories:
---

# 配置 Pods 和容器

## 给容器和 pods 分配内存资源

## 为 Windows Pod 和容器配置 GMSA

## Configure GMSA for Windows Pods and containers

## 为 Windows 的 pod 和容器配置 RunAsUserName

## 配置 Pod 的服务质量

## 为容器分派扩展资源

## 配置 Pod 以使用卷进行存储

## 配置 Pod 以使用 PersistentVolume 作为存储

## 配置 Pod 使用 Projected Volume 作存储

## 为 Pod 或容器配置安全上下文

## 为 Pod 配置服务账户

## 从私有仓库拉取镜像

## 配置 Liveness，Readiness 以及启动探针

这个页面显示了如何配置容器的 liveness,readiness 以及启动的探针

这个[kubelet](https://kubernetes.io/docs/admin/kubelet/)使用 liveness 探针知道什么时候可以重启容器。比如，liveness 探针可以捕获死锁，即应用在运行中但是无法取得进展了。在这种状态下重启容器可以使得尽管有 bugs，但应用仍然可以使用。

这个 kubelet 使用 readiness 探针知道什么时候容器开始准备接收流量了。当 Pod 的所有容器都准备好了之后 Pod 才认为是准备好了。这个信号的一种做法是控制那些 Pod 可以被用作后端服务。当 Pod 没有准备好时，它就会必备从 Service 的负载均衡中删除掉。

kubelet 使用启动探针知道当容器应用已经启动好了。如果这个探针被配置，它会禁用 liveness 和 readiness 检查知道它成功了，保证这些探针不会干扰应用的启动。它可以被用来对很慢的容器启动的 liveness 检查上，避免它在启动运行之前必备 kubelet 杀掉了。

### 在你开始

### 定义 liveness 命令

### 定义 liveness HTTP 请求

### 定义 TCP liveness 探针

### 使用命名 Port

### 使用启动探针保护启动慢的容器

### 定义 readiness 探针

有时候，应用是临时无法响应流量。例如，一个应用可能在启动期间需要加载大量数据或者配置文件，或者在启动之后依赖其他服务。在这种情况下，你不想要杀掉这个应用，但是你也不想要发送请求给它。Kubernetes 提供 readiness 探针来检查和减轻这些场景。pod 的容器上报它没有准备好，没法接受从 Kubernetes 服务来的流量。

> 注意：readiness 探针运行在这个容器整个生命周期中。

readiness 探针配置类似于 liveness 探针。只有一点不同的是使用 readinessProbe 字段替换 livenessProd 字段。

```
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

Http 和 TCP 的 readiness 探针配置也和 liveness 探针相同。

Readiness 和 liveness 可以在同一个容器上并行使用。同时使用这两个可以不保证容器在没有准备好的情况下不接受流量，当它们都失败时才会重启容器。

### 配置探针

## 将 Pod 分配给节点

## 使用节点关联性将 Pod 分配给节点

## 配置 Pod 初始化

## 附加处理者给容器声明周期事件

## 使用 ConfigMap 配置 Pod

## 在 Pod 中的容器之间共享进程命名空间

## 创建静态 Pod

## 将 Docker Compose 文件转换为 Kubernetes 资源
