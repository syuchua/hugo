---
title: 运维岗需要掌握的Redis基础和优化技巧
categories:
  - 笔记
tags:
  - 运维
  - Redis
date: 2024-09-25T23:03:33+08:00
---

---

# Redis 运维笔记

## 目录

1. [Redis 基本概念](#1-redis-基本概念)
2. [Redis 持久化机制](#2-redis-持久化机制)
3. [Redis 高可用](#3-redis-高可用)
4. [Redis 常用命令](#4-redis-常用命令)
5. [Redis 性能优化](#5-redis-性能优化)

---

## 1. Redis 基本概念

### **Redis 是什么？**
- Redis 是一个开源的内存数据结构存储系统，支持多种数据结构如：字符串、哈希、列表、集合、有序集合等。
- Redis 提供持久化机制，可以将内存中的数据保存到磁盘中，重启时加载使用。

### **Redis 应用场景**
- **缓存**: 提高访问速度，减少数据库压力。
- **分布式锁**: 利用 Redis 的原子操作实现高效分布式锁。
- **消息队列**: 利用列表结构实现高效队列。

---

## 2. Redis 持久化机制

Redis 支持两种主要的持久化方式：

### **1. RDB (Redis Database Backup)**
- **原理**: 在指定时间间隔内生成数据快照并保存到磁盘。
- **优点**: RDB 文件紧凑，适合备份。
- **缺点**: 数据不是实时持久化，可能丢失部分数据。

- **配置示例**:
  ```ini
  save 900 1
  save 300 10
  save 60 10000
  ```

### **2. AOF (Append-Only File)**
- **原理**: 记录每次写操作，以日志的形式追加到文件中。
- **优点**: 数据持久化更实时，默认每秒保存一次。
- **缺点**: AOF 文件比 RDB 更大，恢复速度较慢。

- **配置示例**:
  ```ini
  appendonly yes
  appendfsync everysec
  ```

### **选择合适的持久化方式**
- **RDB**: 用于备份，适合在不要求实时性但需要快速恢复的场景。
- **AOF**: 用于数据更实时的持久化，但需要更多的磁盘资源。

---

## 3. Redis 高可用

### **1. 主从复制 (Master-Slave Replication)**
- Redis 支持异步复制，一个 Master 可以有多个 Slave。
- Slave 只读，接收 Master 数据的更新。
- **命令**:
  ```bash
  SLAVEOF <master-ip> <master-port>
  ```

### **2. 哨兵模式 (Sentinel)**
- 实现高可用性，哨兵可以监控主从实例的运行状态，并在主节点故障时自动进行故障转移。
- **优点**: 自动故障切换，监控实例状态。
- **配置文件**:
  ```ini
  sentinel monitor mymaster 127.0.0.1 6379 2
  ```

### **3. Redis 集群 (Redis Cluster)**
- **分布式**: 将数据分布在多个节点上，支持水平扩展。
- **哈希槽 (Hash Slot)**: Redis 集群通过哈希槽将数据分布在多个节点，集群默认有 16384 个槽。
- 支持自动分片和故障转移。

---

## 4. Redis 常用命令

### **键管理**
- 查看所有键:
  ```bash
  KEYS *
  ```

- 删除键:
  ```bash
  DEL <key>
  ```

### **字符串操作**
- 设置键值:
  ```bash
  SET <key> <value>
  ```

- 获取键值:
  ```bash
  GET <key>
  ```

### **列表操作**
- 添加元素到列表:
  ```bash
  LPUSH <key> <value>
  ```

- 获取列表元素:
  ```bash
  LRANGE <key> 0 -1
  ```

### **哈希操作**
- 设置哈希字段:
  ```bash
  HSET <key> <field> <value>
  ```

- 获取哈希字段:
  ```bash
  HGET <key> <field>
  ```

### **集合操作**
- 添加元素到集合:
  ```bash
  SADD <key> <value>
  ```

- 获取集合所有元素:
  ```bash
  SMEMBERS <key>
  ```

### **有序集合操作**
- 添加元素到有序集合:
  ```bash
  ZADD <key> <score> <value>
  ```

- 获取有序集合元素:
  ```bash
  ZRANGE <key> 0 -1
  ```

---

## 5. Redis 性能优化

### **1. 使用缓存**
- 合理设置缓存过期时间，避免占用过多内存。
- 配置示例:
  ```ini
  maxmemory 2gb
  maxmemory-policy allkeys-lru
  ```

### **2. 数据分区**
- 使用 Redis 集群，将数据分散到不同节点，减少单个节点的压力。

### **3. 禁用 AOF 或调整 AOF 策略**
- 如果对数据一致性要求不高，可以禁用 AOF 或调整为 `everysec` 以提高性能。

### **4. 优化 Redis 配置**
- 增加后台线程，调整 `io-threads` 来提升并发性能。
