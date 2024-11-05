---
title: 运维岗需要掌握的K8s基础
categories:
  - 笔记
tags:
  - Kubernetes
  - K8s
  - 运维
date: 2024-09-25T23:02:34+08:00
---

---

# Kubernetes (K8s) 运维笔记

## 目录

1. [Kubernetes 基础概念](#1-kubernetes-基础概念)
2. [Pod 生命周期管理](#2-pod-生命周期管理)
3. [Kubernetes 常用命令](#3-kubernetes-常用命令)
4. [K8s 资源管理](#4-k8s-资源管理)
5. [网络插件](#5-网络插件)
6. [调度与负载均衡](#6-调度与负载均衡)
7. [Kubernetes 高可用性](#7-kubernetes-高可用性)

---

## 1. Kubernetes 基础概念

### **K8s 核心组件**

| 组件           | 描述                                       |
| -------------- | ------------------------------------------ |
| **Pod**        | 最小的部署单元，封装一个或多个容器           |
| **Node**       | 集群中的单个计算节点，运行 Pod 和代理服务     |
| **Cluster**    | 一组 Node，协调资源的调度和管理              |
| **Kube-apiserver** | 集群的入口点，处理 REST 请求               |
| **etcd**       | 用于存储集群的状态数据                       |
| **Kube-scheduler** | 负责 Pod 的调度到 Node                     |
| **Kube-controller-manager** | 负责管理控制器，实现自动化控制      |
| **Kubelet**    | 运行在每个 Node 上，管理 Pod 和容器          |
| **Kube-proxy** | 处理网络规则，负载均衡 Pod 请求              |

---

## 2. Pod 生命周期管理

### **Pod 生命周期阶段**
- **Pending**: Pod 被创建，但未绑定到 Node。
- **Running**: Pod 已绑定到 Node 并启动了容器。
- **Succeeded**: 所有容器成功完成并退出。
- **Failed**: 某些容器异常终止并退出。
- **Unknown**: 无法与 Node 通信。

### **Pod 生命周期管理组件**

| 组件            | 描述                                              |
| --------------- | ------------------------------------------------- |
| **Init Containers** | 初始化容器，确保主容器启动前某些任务完成          |
| **Liveness Probe**  | 健康检查，确保容器存活                           |
| **Readiness Probe** | 就绪检查，确保容器可接受流量                     |
| **Startup Probe**   | 启动检查，确保容器启动后运行稳定                 |

---

## 3. Kubernetes 常用命令

### **集群管理命令**
- 查看集群状态
  ```bash
  kubectl cluster-info
  ```

- 查看所有 Node
  ```bash
  kubectl get nodes
  ```

- 获取 Pod 详情
  ```bash
  kubectl describe pod <pod_name>
  ```

### **Pod 操作命令**
- 查看所有 Pod
  ```bash
  kubectl get pods
  ```

- 创建 Pod
  ```bash
  kubectl run <pod_name> --image=<image_name>
  ```

- 删除 Pod
  ```bash
  kubectl delete pod <pod_name>
  ```

### **其他常用命令**
- 获取 Service 列表
  ```bash
  kubectl get svc
  ```

- 获取 Deployment 列表
  ```bash
  kubectl get deployment
  ```

---

## 4. K8s 资源管理

### **资源种类**
- **Pod**: Kubernetes 中最小的部署单元。
- **Service**: 用于暴露 Pod 服务，支持负载均衡。
- **ConfigMap**: 存储配置信息，用于 Pod 内配置文件。
- **Secret**: 安全存储敏感信息，例如密码和 API 密钥。
- **Volume**: 用于持久化存储，Pod 重启后数据不会丢失。

### **命令示例**
- 创建 Deployment
  ```bash
  kubectl create deployment <name> --image=<image>
  ```

- 删除资源
  ```bash
  kubectl delete <resource_type> <resource_name>
  ```

---

## 5. 网络插件

### **常见网络插件**
- **Flannel**: 最基础的网络插件，简单易用。
- **Calico**: 支持网络策略，灵活且性能好。
- **Weave**: 支持自动网络发现和加密。
- **Cilium**: 基于 eBPF，支持细粒度的安全控制。

### **插件管理**
- 安装 Flannel
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
  ```

- 查看网络状态
  ```bash
  kubectl get pods -n kube-system
  ```

---

## 6. 调度与负载均衡

### **K8s 调度算法**
- **RR (Round Robin)**: 按顺序将请求调度到不同节点。
- **Least Connections**: 将请求调度到连接数最少的节点。
- **Resource-Based**: 根据节点资源使用情况进行调度。

### **负载均衡**
- Kubernetes 内置 **Service** 对 Pod 进行负载均衡。
- 使用 **Ingress** 管理外部 HTTP 和 HTTPS 访问。

---

## 7. Kubernetes 高可用性

### **高可用架构**
- **Master 高可用**: 通过多 Master 节点保证控制平面高可用。
- **etcd 高可用**: 使用 etcd 集群存储数据，保证高可用。
- **Node 高可用**: 通过节点间冗余，Pod 可以在不同节点上运行，避免单点故障。

### **水平扩展**
- 使用 Horizontal Pod Autoscaler (HPA) 根据负载自动扩展 Pod。

