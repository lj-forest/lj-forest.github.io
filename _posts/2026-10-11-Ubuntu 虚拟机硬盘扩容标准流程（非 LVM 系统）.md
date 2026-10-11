```
layout: post
title: "🎯 Ubuntu 虚拟机硬盘扩容标准流程（非 LVM 系统）"
date: 2026-10-11 12:02:00 +0800
categories: 教程
```

### 🎯 Ubuntu 虚拟机硬盘扩容标准流程（非 LVM 系统）

**前置条件**：已经在 VMware 中关机，并将虚拟硬盘扩展到了 60G。

**第一步：检查磁盘现状**

```
lsblk
```

*确认 `sda` 总大小已变为 60G，但 `sda3` 还是原来的大小*

**第二步：安装分区扩展工具**

```
sudo apt update
sudo apt install -y cloud-guest-utils
```

**第三步：扩展分区（填入剩余空间）**

```
sudo growpart /dev/sda 3
```

*注意：`/dev/sda` 和 `3` 之间**有一个空格**。如果你的根分区编号不是 3，请根据 `lsblk` 输出修改。*

**第四步：直接扩展文件系统（不需要 LVM）**

```
sudo resize2fs /dev/sda3
```

**第五步：验证结果**

```
df -h
```

*只要看到 `/dev/sda3` 挂载的 `/` 变成了 59G 左右，就大功告成了。*