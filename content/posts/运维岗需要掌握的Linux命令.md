---
title: 运维岗需要掌握的Linux命令
categories:
  - 笔记
tags:
  - 运维
  - Linux
  - Linux常用命令
date: 2024-09-25T23:02:00+08:00
---

---

# Linux 基础运维笔记

## 目录

1. [RPM 包管理](#1-rpm-包管理)
2. [YUM 包管理](#2-yum-包管理)
3. [文件系统管理](#3-文件系统管理)
4. [LVM 动态扩容](#4-lvm-动态扩容)
5. [系统负载查看](#5-系统负载查看)
6. [磁盘管理与分区](#6-磁盘管理与分区)
7. [进程管理](#7-进程管理)
8. [网络管理](#8-网络管理)
9. [安全管理](#9-安全管理)
10. [日志管理](#10-日志管理)

---

## 1. RPM 包管理

### **常用命令**

| 操作          | 命令                                    |
| ------------- | --------------------------------------- |
| 查看包文件列表 | `rpm -ql <package_name>`                |
| 安装包        | `rpm -ivh <package.rpm>`                |
| 升级包        | `rpm -Uvh <package.rpm>`                |
| 卸载包        | `rpm -e <package_name>`                 |
| 查询已安装包  | `rpm -qa` | `grep <package_name>`       |
| 检查文件来源  | `rpm -qf <file_path>`                   |
| 验证包        | `rpm -V <package_name>`                 |

### **示例**
```bash
rpm -ql httpd
```
```bash
rpm -ivh nginx-1.20.1.rpm
```

---

## 2. YUM 包管理

### **常用命令**

| 操作                | 命令                                      |
| ------------------- | ----------------------------------------- |
| 列出可用包          | `yum list available`                      |
| 安装包              | `yum install <package_name>`              |
| 更新系统中所有包    | `yum update`                              |
| 卸载包              | `yum remove <package_name>`               |
| 查看包信息          | `yum info <package_name>`                 |
| 列出已安装的包      | `yum list installed`                      |
| 清除缓存            | `yum clean all`                           |

### **示例**
```bash
yum install httpd
```
```bash
yum remove httpd
```

---

## 3. 文件系统管理

### **挂载与卸载文件系统**
- **挂载文件系统**
  ```bash
  mount <device> <mount_point>
  ```
  示例：
  ```bash
  mount /dev/sda1 /mnt
  ```

- **卸载文件系统**
  ```bash
  umount <mount_point>
  ```
  示例：
  ```bash
  umount /mnt
  ```

- **查看当前挂载的文件系统**
  ```bash
  df -h
  ```

### **文件系统的创建**
- **创建 ext4 文件系统**
  ```bash
  mkfs.ext4 /dev/sdX
  ```

- **创建 xfs 文件系统**
  ```bash
  mkfs.xfs /dev/sdX
  ```

---

## 4. LVM 动态扩容

### **步骤**
1. **创建物理卷**
   ```bash
   pvcreate /dev/sdX
   ```

2. **创建卷组**
   ```bash
   vgcreate <volume_group_name> /dev/sdX
   ```

3. **创建逻辑卷**
   ```bash
   lvcreate -L <size> -n <logical_volume_name> <volume_group_name>
   ```

4. **扩展逻辑卷**
   ```bash
   lvextend -L +<size> /dev/<volume_group>/<logical_volume>
   ```

5. **扩展文件系统**
   - 对 ext4 文件系统：
     ```bash
     resize2fs /dev/<volume_group>/<logical_volume>
     ```

   - 对 xfs 文件系统：
     ```bash
     xfs_growfs /dev/<volume_group>/<logical_volume>
     ```

---

## 5. 系统负载查看

### **常用命令**

| 命令         | 描述                                      |
| ------------ | ----------------------------------------- |
| `top`        | 实时查看系统状态，包括 CPU、内存、任务    |
| `htop`       | `top` 的增强版，提供更丰富的界面和操作    |
| `uptime`     | 查看系统启动时间、负载信息                |
| `vmstat`     | 查看系统虚拟内存、IO、进程等信息          |
| `iostat`     | 查看磁盘 I/O 使用情况                     |
| `sar`        | 收集系统性能数据，需安装 `sysstat`        |

### **示例**
```bash
top
```
```bash
uptime
```
```bash
vmstat
```

---

## 6. 磁盘管理与分区

### **查看磁盘使用情况**
```bash
df -h
```

### **查看磁盘 I/O 活动**
```bash
iostat
```

### **磁盘分区工具**
- **`fdisk`**：适用于 MBR 分区格式磁盘
  ```bash
  fdisk /dev/sda
  ```

- **`parted`**：适用于 GPT 分区格式磁盘
  ```bash
  parted /dev/sda
  ```

### **创建分区**
1. **使用 `fdisk` 创建分区**
   ```bash
   fdisk /dev/sda
   ```

2. **使用 `parted` 创建 GPT 分区**
   ```bash
   parted /dev/sda mklabel gpt
   parted /dev/sda mkpart primary 0% 100%
   ```

---

## 7. 进程管理

### **查看进程**
```bash
ps -aux
```

### **查看进程树**
```bash
pstree
```

### **杀死进程**
- **正常终止**
  ```bash
  kill <pid>
  ```

- **强制杀死**
  ```bash
  kill -9 <pid>
  ```

---

## 8. 网络管理

### **查看网络配置**
```bash
ifconfig
```

### **查看路由表**
```bash
route -n
```

### **测试网络连通性**
```bash
ping <hostname or IP>
```

### **检查端口状态**
```bash
netstat -tuln
```

---

## 9. 安全管理

### **防火墙管理**

| 命令         | 描述                                      |
| ------------ | ----------------------------------------- |
| `firewalld`  | 动态防火墙管理工具                        |
| `iptables`   | 传统防火墙规则配置工具                    |

- **Firewalld 命令**
  - 启动 Firewalld：
    ```bash
    systemctl start firewalld
    ```
  - 允许某端口（如 80 端口）：
    ```bash
    firewall-cmd --permanent --add-port=80/tcp
    ```
  - 重启防火墙：
    ```bash
    firewall-cmd --reload
    ```

- **iptables 命令**
  - 查看规则：
    ```bash
    iptables -L
    ```
  - 允许某端口（如 80 端口）：
    ```bash
    iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    ```

### **用户与权限管理**

- **创建用户**
  ```bash
  useradd <username>
  ```

- **删除用户**
  ```bash
  userdel <username>
  ```

- **修改用户密码**
  ```bash
  passwd <username>
  ```

- **查看当前用户**
  ```bash
  whoami
  ```

- **更改文件权限**
  ```bash
  chmod 755 <file> 
  ```

- **更改文件属主**
  ```bash
  chown <owner>:<group> <file>
  ```

---

## 10. 日志管理

### **常见日志文件**

| 日志文件              | 描述                                      |
| --------------------- | ----------------------------------------- |
| `/var/log/messages`   | 通用系统日志                              |
| `/var/log/secure`     | 安全相关日志（如登录、sudo 操作等）        |
| `/var/log/dmesg`      | 内核日志                                  |
| `/var/log/boot.log`   | 启动日志                                  |
| `/var/log/httpd/`     | Apache 日志文件夹                         |
| `/var/log/nginx/`     | Nginx 日志文件夹                          |

### **日志查看与监控**

- **查看日志**
  ```bash
  cat /var/log/messages
  ```

- **实时查看日志**
  ```bash
  tail -f /var/log/messages
  ```