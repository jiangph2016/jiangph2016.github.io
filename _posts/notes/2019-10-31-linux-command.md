---
layout: post
title: Linux功能命令记录
category: 笔记
tags: Linux
keywords: Linux
---


* content
{:toc}

随时记录，遇到一个记一个
## 查询系统信息

### 查看当前文件夹下的各个文件的大小

```
du -h --max-depth=1
```

### 当前系统可用容量

```
df -h
```
### 当前内存使用量

```
free -mh
```

### 列举每根内存条及容量

```
dmidecode -t memory | grep -i size
```

### 计算机可以安装的最大内存
```
dmidecode -t memory | grep -i max
```

### 所有磁盘及其分区的大小

```
lsblk
```

### 默认网关和路由表

```
ip route | column -t
```
或
```
netstat -r
```
### 查看网络接口
```
ip link show
```
或
```
netstat -i
```
### 关于磁盘、扇区
```
fdisk -l
```

## find命令

最常见的按名称查找，格式：
```
find [位置] -name [匹配符]
```