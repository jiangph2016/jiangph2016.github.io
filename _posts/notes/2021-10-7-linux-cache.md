---
layout: post
title: 【Linux】Linux清除缓存占用
category: 笔记
tags: Linux
keywords: Linux
---

* content
{:toc}


## 查看占用

- 一般用`df -h`命令查看整体的占用，当发现满了的时候需要排查来清理空间  
- `du -sh`查看当前目录的占用总和  
- `du -h --max-depth=1`查看当前目录下每一级目录的占用总和，比较常用

- `find . -size +200M` 查找占用超过200M的文件

## yum缓存

缓存目录: `/var/cache/yum`  

清理命令 :`yum clean all`

## docker占用

docker的镜像和缓存会占用大量的存储空间

目录: `/var/lib/docker/overlay2`  
通过`docker system df`命令可以查看docker各种镜像和容器的空间占用   
`docker system df -v`列出各个镜像具体的占用情况  


清理命令`docker system prune`
- 清理所有停止的容器
- 未被使用的网络
- 所有dangling状态的镜像
- 所有dangling状态的build cache


