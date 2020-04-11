---
title: 工作负载
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-11 13:31:36
password:
summary:
tags: [k8s, concepts, 翻译, 进行中]
categories:
---

## Pods

### Pod 概览

### Pods

### Pod 生命周期

### 初始化容器

### Pod 预设置

### Pod 拓扑展开约束

### 破坏

### 暂时容器

## 控制器

### ReplicaSet

ReplicaSet 的目的是在运行的任何时间内都稳定运行一定数量的 pod。因此，它经常用来保证指定 pod 数量的可用性。

#### ReplicaSet 如何工作

#### 什么时候使用 ReplicaSet

ReplicaSet 保证在任何给定时间都会复制给定数量的 pod。然而，Deployment 是一个更高级概念，用于管理 ReplicaSet 并提供 Pod 的声明性更新和其他拥有的功能。但是我们建议使用 Deployment 而不是直接使用 ReplicaSets，除非你要求自定义更新安排，而不需要全部更新。

这意味着你不需要维护 ReplicaSet 对象：使用 Deployment 代替，并在应用的 Spec 一节里定义。

**示例**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # 根据你的场景修改 replicas
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
        - name: php-redis
          image: gcr.io/google_samples/gb-frontend:v3
```

保存这个清单文件到 frontend.yaml 并提交它到 Kubernetes 集群，它将创建 ReplicaSet 的定义和它要管理的 Pods。

```shell
kubectl apply -f https://kubernetes.io/examples/controllers/frontend.yaml
```

你然后可以获取当前部署的 ReplicaSets

```shell
kubectl get rs
```

并查看你创建的前端应用

```
NAME       DESIRED   CURRENT   READY   AGE
frontend   3         3         3       6s
```

你也可以检查 ReplicaSet 的状态

```shell
kubectl describe rs/frontend
```

然后你将看到类似的输出：

```
Name:         frontend
Namespace:    default
Selector:     tier=frontend
Labels:       app=guestbook
              tier=frontend
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"apps/v1","kind":"ReplicaSet","metadata":{"annotations":{},"labels":{"app":"guestbook","tier":"frontend"},"name":"frontend",...
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  tier=frontend
  Containers:
   php-redis:
    Image:        gcr.io/google_samples/gb-frontend:v3
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  117s  replicaset-controller  Created pod: frontend-wtsmm
  Normal  SuccessfulCreate  116s  replicaset-controller  Created pod: frontend-b2zdv
  Normal  SuccessfulCreate  116s  replicaset-controller  Created pod: frontend-vcmts
```

最后你可以查看成长的 Pods

```shell
kubectl get pods
```

你应该看到 Pod 信息类似于：

```
NAME             READY   STATUS    RESTARTS   AGE
frontend-b2zdv   1/1     Running   0          6m36s
frontend-vcmts   1/1     Running   0          6m36s
frontend-wtsmm   1/1     Running   0          6m36s
```

你也可以验证这些 Pods 的所有者的引用是否设置为了前端的 ReplicaSet。为了做到它，从运行的一个 Pod 获取 yaml 文件。

```shell
kubectl get pods frontend-b2zdv -o yaml
```

打印的输出类似这个，这个前端应用 ReplicaSet 的信息被设置在 ownerReferences 的元数据字段上。

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2020-02-12T07:06:16Z"
  generateName: frontend-
  labels:
    tier: frontend
  name: frontend-b2zdv
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: frontend
    uid: f391f6db-bb9b-4c09-ae74-6a1f77f3d5cf
...
```

#### 例子

#### 没有模板的 Pod acquisitions

#### 编写 ReplicaSet 的清单文件

#### 使用 ReplicaSets

#### 替代 ReplicaSet
